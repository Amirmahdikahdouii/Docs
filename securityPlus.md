# Security+

This repository includes all references and techniques that I have been learned during the security+ course.

## homograph attack

> [!NOTE]
> Homograph attacks are a type of attacks that by change some special characters and use the ascii code, you visually see something but in background, there is something else!
> For example, `google.com` could be `gооgle.ϲоm` but in background the `o` and `c` are not the same and they have differences in unicode structure.

---

## 🧠 **Common Unicode Characters Used in URL Deception**

| **Look-alike Character** | **Description**                    | **Unicode** | **Script** | **Example Target** |
|--------------------------|-------------------------------------|-------------|------------|---------------------|
| `a` → `а`                | Cyrillic Small Letter A            | U+0430      | Cyrillic   | `apple.com`         |
| `c` → `ϲ`                | Greek Small Letter Sigma            | U+03F2      | Greek      | `coinbase.com`      |
| `e` → `е`                | Cyrillic Small Letter IE           | U+0435      | Cyrillic   | `netflix.com`       |
| `i` → `і`                | Cyrillic Small Letter Byelorussian-Ukrainian I | U+0456 | Cyrillic | `linkedin.com`      |
| `o` → `о`                | Cyrillic Small Letter O            | U+043E      | Cyrillic   | `google.com`        |
| `p` → `р`                | Cyrillic Small Letter ER           | U+0440      | Cyrillic   | `paypal.com`        |
| `s` → `ѕ`                | Cyrillic Small Letter Dze          | U+0455      | Cyrillic   | `spotify.com`       |
| `x` → `х`                | Cyrillic Small Letter HA           | U+0445      | Cyrillic   | `x.com`              |
| `y` → `у`                | Cyrillic Small Letter U            | U+0443      | Cyrillic   | `youtube.com`        |
| `B` → `Β`                | Greek Capital Letter Beta          | U+0392      | Greek      | `BBC.com`            |
| `K` → `Κ`                | Greek Capital Letter Kappa         | U+039A      | Greek      | `Kaspersky.com`      |
| `H` → `Н`                | Cyrillic Capital Letter EN         | U+041D      | Cyrillic   | `Hulu.com`           |
| `M` → `М`                | Cyrillic Capital Letter EM         | U+041C      | Cyrillic   | `Microsoft.com`      |
| `P` → `Р`                | Cyrillic Capital Letter ER         | U+0420      | Cyrillic   | `PayPal.com`         |
| `C` → `С`                | Cyrillic Capital Letter ES         | U+0421      | Cyrillic   | `Cisco.com`          |
| `T` → `Τ`                | Greek Capital Letter Tau           | U+03A4      | Greek      | `Twitter.com`        |

---

## 🧨 **Invisible & Special Characters**

| **Character**                 | **Purpose**                  | **Unicode** | **Effect**                      |
|--------------------------------|-------------------------------|-------------|----------------------------------|
| Zero Width Space              | `​`                           | U+200B      | Invisible spacing               |
| Zero Width Non-Joiner         | `‌`                           | U+200C      | Breaks ligatures invisibly      |
| Zero Width Joiner             | `‍`                           | U+200D      | Joins letters invisibly         |
| Left-to-Right Mark (LRM)       | `‎`                           | U+200E      | Alters text direction subtly    |
| Right-to-Left Mark (RLM)       | `‏`                           | U+200F      | Changes flow of text            |
| Soft Hyphen                   | `­`                           | U+00AD      | Invisible unless line break occurs |

---

## Google Dorking

See this link from [imperva](https://www.imperva.com/learn/application-security/google-dorking-hacking/).
