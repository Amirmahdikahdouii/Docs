---
title: "Understanding Pseudo-Random String Generation in Go and Its Pitfalls"
---
# Understanding Pseudo-Random String Generation in Go and Its Pitfalls

## 1. What Was the Problem?

The issue you encountered emerged from a Go application generating pseudo-random numeric strings using the `math/rand` package. Over time, the random strings started to become highly similar, especially filled with zeros, leading to a significant increase in collisions (i.e., generating the same string multiple times).

Here’s an example of problematic strings:

```
000000400000004800000000000000
000000060000000000000000000000
400000000000000800000000000000
700000000000000000000010000000
```

These strings demonstrate a pattern of having many `0`s and only a few digits varying, which compromises their usefulness in contexts where unique, unpredictable strings are expected (e.g., session tokens, temporary identifiers).

The root of the problem lies in how randomness was seeded and utilized using a Linear Congruential Generator (LCG), along with how bits were extracted and mapped to characters.

---

## 2. Explanation of the Random String Generator Function

Let’s dissect the function step-by-step:

```go
var src = rand.NewSource(time.Now().UnixNano())
const (
    letterIdxBits = 6                    // 6 bits to represent a letter index
    letterIdxMask = 1<<letterIdxBits - 1 // All 1-bits mask, e.g., 63
    letterIdxMax  = 63 / letterIdxBits   // # of letter indices in a 63-bit int
)

func GetRandomString(n int) string {
    const letters = "0123456789"
    b := make([]byte, n)

    for i, cache, remain := n-1, src.Int63(), letterIdxMax; i >= 0; {
        if remain == 0 {
            cache, remain = src.Int63(), letterIdxMax
        }
        if idx := int(cache & letterIdxMask); idx < len(letters) {
            b[i] = letters[idx]
            i--
        }
        cache >>= letterIdxBits
        remain--
    }

    return *(*string)(unsafe.Pointer(&b))
}
```

### Breakdown:

* `letters = "0123456789"`: This is the character set, i.e., digits from 0 to 9.
* `b := make([]byte, n)`: Allocate a byte slice to store the result.

#### The Loop and Bit Extraction

```go
for i, cache, remain := n-1, src.Int63(), letterIdxMax; i >= 0; {
```

* `src.Int63()` gives a 63-bit random number.
* `cache` temporarily holds the bits.
* `remain` tracks how many 6-bit chunks are left.

The critical line is:

```go
if idx := int(cache & letterIdxMask); idx < len(letters) {
    b[i] = letters[idx]
    i--
}
```

* `letterIdxBits = 6` (i.e., each character index is taken from 6 bits).
* `letterIdxMask = 1<<6 - 1 = 0b111111 = 63` (used to mask the last 6 bits).

### How It Works

`cache & letterIdxMask` isolates the **last 6 bits** of `cache`:

* This gets a number between `0` and `63`.
* If the number is less than `10` (length of `letters`), it’s used as an index.

### Efficiency Trick

Using 63 bits in `cache` is a performance optimization. Instead of calling `src.Int63()` `n` times, the function reuses a single 63-bit value to produce several characters (each 6 bits).

However, only values `0–9` are valid indices. So \~84% of bits are discarded:

* From `0–63`, only `0–9` are valid.
* Acceptance rate = `10/64 ≈ 15.6%`.
* Rejection rate = `84.4%`.

This is a classic example of a **rejection sampling** technique.

---

## 3. What Caused the Problem?

Over time, you observed that the generated strings had many zeros and were similar, which increased the probability of collision.

This problem arises because of **two key issues**:

### 1. **Static Seeded LCG Generator**

The line:

```go
var src = rand.NewSource(time.Now().UnixNano())
```

is called once and holds the seed for the entire application’s lifetime. This results in a deterministic sequence of values (from the Linear Congruential Generator). If your app runs for a long time and generates many strings, it will eventually produce similar values.

### 2. **Inefficient Mapping**

From each `cache`, you extract 6-bit chunks and only use those that are in `0–9`. Because only \~16% of values are accepted, you’re wasting a lot of entropy:

* You’re pulling 63 bits.
* On average, only 9.8 bits (15.6% of 63) are useful.

So the chance that you land in the `0` index (which maps to `'0'`) happens far more frequently than expected if the RNG becomes biased or exhausted due to the LCG cycle.

---

## 4. How Does the LCG Work Behind the Scenes?

### What is LCG?

A Linear Congruential Generator is a type of pseudo-random number generator defined by the recurrence relation:

```
Xₙ₊₁ = (aXₙ + c) mod m
```

Where:

* `X₀` is the seed.
* `a`, `c`, and `m` are constants.

In Go’s `math/rand` implementation:

```go
Xₙ₊₁ = (6364136223846793005 * Xₙ + 1) % 2⁶³
```

* This gives 63-bit integers (`Int63()`).

### Key Characteristics

* Deterministic: Same seed → same sequence.
* Periodic: Has a maximum period of `2⁶³`, after which numbers start repeating.
* Low entropy over time when misused.

Because LCGs have poor entropy in lower bits and you’re constantly sampling only the lowest 6 bits (`cache & letterIdxMask`), the poor randomness compounds over time.

### Why Zeros?

Over time:

* The entropy of the LCG may degrade if you overuse the lower bits.
* You keep extracting the lowest 6 bits (`cache & 63`) and shifting bits right.
* Since `0` is at index 0 in `letters`, any bias toward small values shows up as more `'0'`s in your output.

---

## 5. Fixes and Recommendations

### Option 1: Use `rand.Read()`

Use cryptographically secure randomness with `crypto/rand`:

```go
func GetRandomDigits(length int) (string, error) {
    const digits = "0123456789"
    b := make([]byte, length)
    if _, err := rand.Read(b); err != nil {
        return "", err
    }
    for i := 0; i < length; i++ {
        b[i] = digits[b[i]%10]
    }
    return string(b), nil
}
```

* This avoids LCG and uses a cryptographically secure PRNG.
* All bits are equally random.

### Option 2: Use `rand.New(rand.NewSource(seed))` Per Call

If you're okay with `math/rand`, reinitialize the generator each time:

```go
func GetRandomString(n int) string {
    const letters = "0123456789"
    b := make([]byte, n)
    r := rand.New(rand.NewSource(time.Now().UnixNano()))
    for i := 0; i < n; i++ {
        b[i] = letters[r.Intn(len(letters))]
    }
    return string(b)
}
```

* Avoids reusing a long-lived, exhausted LCG.
* Simplifies bit logic.

### Option 3: Pre-shuffle Digits from a Seeded Pool

If high performance is critical and cryptographic randomness is overkill:

* Generate a pool of values.
* Shuffle them per request or refresh periodically.

---

## Final Thoughts

Your initial implementation, although efficient in memory and CPU, relied too heavily on reuse of a seeded LCG and bit manipulation, which over time resulted in degraded randomness.

The critical problems:

* Reuse of a single LCG seed.
* Heavy rejection sampling.
* Extraction of only low 6-bit chunks.

If your application needs reliable uniqueness and randomness over long uptime periods, favor:

* Short-lived seeds, or
* True randomness using `crypto/rand`, or
* A better PRNG (e.g., xoshiro256\*\*, PCG, or using `math/rand` correctly).

Randomness is subtle and fragile; a good solution always comes from understanding the algorithm under the hood.

---

## References

* Go’s `math/rand` documentation: [https://pkg.go.dev/math/rand](https://pkg.go.dev/math/rand)
* Go’s `crypto/rand` documentation: [https://pkg.go.dev/crypto/rand](https://pkg.go.dev/crypto/rand)
* Linear Congruential Generator: [https://en.wikipedia.org/wiki/Linear\_congruential\_generator](https://en.wikipedia.org/wiki/Linear_congruential_generator)
* Random number generator seeding pitfalls: [https://nullprogram.com/blog/2017/09/21/](https://nullprogram.com/blog/2017/09/21/)
