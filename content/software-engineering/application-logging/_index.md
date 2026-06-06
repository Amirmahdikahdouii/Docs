---
title: "Complete Guide to Go Application Logging for Monitoring"
---
# Complete Guide to Go Application Logging for Monitoring

This article is AI generated, the prompt for getting this result by `Calude Sonnet 4` is:

```markdown
I want  to improve my Golang application logging, so I can collect them somewhere using monitoring tools, and then use this log in monitoring.
I want to log efficient and detailed but minimized, So can you help me through this?
* Say to me that how should I log structured, to be able to use by monitoring and log collector tools
* teach me how to use frameworks for logging
* tell me what should I log and what I shouldn't
* tell me about monitoring logs and available tools for it
* tell me what exactly huge companies set loggers in their apps and which log their store for monitoring 

I want you to answer me in detail and complete
```

## 1. Structured Logging for Monitoring Tools

### Why Structured Logging?
Structured logging outputs logs in a consistent, machine-readable format (usually JSON) that monitoring tools can easily parse, index, and query. This is essential for modern observability.

### Key Principles for Monitoring-Ready Logs:

**Consistent Format**: Use JSON format with standardized field names
```json
{
  "timestamp": "2025-08-14T10:30:00Z",
  "level": "info",
  "message": "User login successful",
  "service": "auth-service",
  "version": "1.2.3",
  "user_id": "12345",
  "request_id": "req-abc123",
  "duration_ms": 150,
  "http_status": 200
}
```

**Standard Fields Every Log Should Have**:
- `timestamp`: ISO 8601 format
- `level`: debug, info, warn, error, fatal
- `message`: Human-readable description
- `service`: Service/application name
- `version`: Application version
- `environment`: dev, staging, prod
- `request_id`: Unique identifier for request tracing

**Context Fields** (when applicable):
- `user_id`: For user-related operations
- `session_id`: For session tracking
- `correlation_id`: For distributed tracing
- `duration_ms`: For performance monitoring
- `error_code`: Standardized error codes
- `http_method`, `http_status`, `endpoint`: For HTTP operations

## 2. Go Logging Frameworks

### Recommended Frameworks:

#### **Logrus** - Feature Rich & Popular
```go
package main

import (
    "github.com/sirupsen/logrus"
)

func setupLogrus() {
    logrus.SetFormatter(&logrus.JSONFormatter{
        TimestampFormat: "2006-01-02T15:04:05Z07:00",
        FieldMap: logrus.FieldMap{
            logrus.FieldKeyTime: "timestamp",
            logrus.FieldKeyLevel: "level",
            logrus.FieldKeyMsg: "message",
        },
    })
    
    logrus.SetLevel(logrus.InfoLevel)
}

func example() {
    logrus.WithFields(logrus.Fields{
        "user_id": "12345",
        "request_id": "req-abc123",
        "duration_ms": 150,
    }).Info("User login successful")
}
```

#### **Zap** - High Performance (Uber's Logger)
```go
package main

import (
    "go.uber.org/zap"
    "go.uber.org/zap"
)

func setupZap() *zap.Logger {
    config := zap.NewProductionConfig()
    config.OutputPaths = []string{"stdout"}
    
    logger, _ := config.Build()
    return logger
}

func example() {
    logger := setupZap()
    defer logger.Sync()
    
    logger.Info("User login successful",
        zap.String("user_id", "12345"),
        zap.String("request_id", "req-abc123"),
        zap.Int("duration_ms", 150),
    )
}
```

#### **Zerolog** - Zero Allocation Logger
```go
package main

import (
    "github.com/rs/zerolog"
    "github.com/rs/zerolog/log"
    "os"
)

func setupZerolog() {
    zerolog.TimeFieldFormat = zerolog.TimeFormatUnix
    log.Logger = log.Output(zerolog.ConsoleWriter{Out: os.Stderr})
}

func example() {
    log.Info().
        Str("user_id", "12345").
        Str("request_id", "req-abc123").
        Int("duration_ms", 150).
        Msg("User login successful")
}
```

#### **slog** - Go 1.21+ Built-in Structured Logger
```go
package main

import (
    "log/slog"
    "os"
)

func setupSlog() {
    logger := slog.New(slog.NewJSONHandler(os.Stdout, &slog.HandlerOptions{
        Level: slog.LevelInfo,
    }))
    slog.SetDefault(logger)
}

func example() {
    slog.Info("User login successful",
        "user_id", "12345",
        "request_id", "req-abc123",
        "duration_ms", 150,
    )
}
```

### Framework Comparison:
- **Zap**: Best performance, slightly complex API
- **Zerolog**: Good performance, fluent API, zero allocations
- **Logrus**: Most popular, easy to use, rich ecosystem
- **slog**: Standard library, simple, good enough for most cases

## 3. What to Log and What Not to Log

### ✅ **WHAT TO LOG:**

**Application Events**:
- Application startup/shutdown
- Service health status changes
- Configuration changes
- Feature flag changes

**User Actions** (without sensitive data):
- Login/logout attempts
- Key business operations
- Permission changes
- Account modifications

**System Operations**:
- Database connections/disconnections
- External API calls
- Cache hits/misses
- Queue operations

**Performance Metrics**:
- Request duration
- Database query times
- External API response times
- Memory usage spikes

**Errors and Warnings**:
- All errors with context
- Validation failures
- Timeout events
- Rate limiting triggers

**Security Events**:
- Authentication failures
- Authorization denials
- Suspicious activities
- Security policy violations

### ❌ **WHAT NOT TO LOG:**

**Sensitive Information**:
- Passwords (never!)
- API keys, tokens, secrets
- Credit card numbers
- SSNs, personal identifiers
- Private user data

**High-Frequency, Low-Value Events**:
- Every successful health check
- Routine background tasks
- Heartbeat messages
- Debug information in production

**Large Payloads**:
- Full request/response bodies
- Large file contents
- Complete database records
- Binary data

## 4. Log Monitoring and Available Tools

### **Log Collection Tools:**

#### **Fluent Bit / Fluentd**
- Lightweight log processor
- Multiple input/output plugins
- Low memory footprint
- Kubernetes native

#### **Filebeat (Elastic Stack)**
- Part of ELK stack
- Lightweight shipper
- Built-in modules for common apps
- Reliable delivery

#### **Promtail (Grafana Loki)**
- Log agent for Loki
- Label-based indexing
- Cost-effective storage
- Integrates with Grafana

#### **Vector**
- High-performance observability data pipeline
- Transform and route logs
- Multiple sources and sinks

### **Log Storage and Analysis:**

#### **ELK Stack (Elasticsearch, Logstash, Kibana)**
- Full-text search capabilities
- Rich visualization in Kibana
- Scalable and powerful
- Complex to manage

#### **Grafana Loki + Grafana**
- Label-based indexing (like Prometheus)
- Cost-effective storage
- Excellent integration with metrics
- Simpler than ELK

#### **Splunk**
- Enterprise-grade
- Powerful search and analytics
- Expensive but feature-rich
- Machine learning capabilities

#### **Cloud Solutions**:
- **AWS CloudWatch Logs**
- **Google Cloud Logging**
- **Azure Monitor Logs**
- **Datadog Logs**
- **New Relic Logs**

### **Monitoring and Alerting:**

#### **Prometheus + Alertmanager**
- Metrics from log aggregations
- Flexible alerting rules
- Integration with Grafana

#### **Grafana Alerting**
- Visual alert rule creation
- Multiple notification channels
- Template-based notifications

### **Example Monitoring Stack Setup:**

```yaml
# docker-compose.yml for local development
version: '3.8'
services:
  loki:
    image: grafana/loki:2.9.0
    ports:
      - "3100:3100"
    volumes:
      - ./loki-config.yml:/etc/loki/local-config.yaml
    command: -config.file=/etc/loki/local-config.yaml

  promtail:
    image: grafana/promtail:2.9.0
    volumes:
      - /var/log:/var/log:ro
      - ./promtail-config.yml:/etc/promtail/config.yml
    command: -config.file=/etc/promtail/config.yml

  grafana:
    image: grafana/grafana:10.0.0
    ports:
      - "3000:3000"
    environment:
      - GF_SECURITY_ADMIN_PASSWORD=admin
    volumes:
      - grafana-storage:/var/lib/grafana
```

## 5. Enterprise Logging Strategies

### **How Big Companies Handle Logging:**

#### **Google**
- **Strategy**: Centralized logging with structured data
- **Tools**: Internal tools (Borgmon, Monarch) + Cloud Logging
- **Scale**: Processes petabytes of logs daily
- **Key Practices**:
  - Standardized log formats across all services
  - Automatic log retention policies
  - Machine learning for anomaly detection

#### **Netflix**
- **Strategy**: Real-time log processing and analysis
- **Tools**: Custom tools + ELK Stack + Kafka
- **Scale**: Billions of events per day
- **Key Practices**:
  - Event-driven architecture
  - Real-time alerting
  - A/B testing through logs

#### **Uber**
- **Strategy**: High-performance logging with Zap
- **Tools**: Zap + Jaeger + custom analytics
- **Scale**: Millions of trips generate massive logs
- **Key Practices**:
  - Zero-allocation logging
  - Distributed tracing integration
  - Business metric extraction from logs

#### **Amazon**
- **Strategy**: Service-oriented logging
- **Tools**: CloudWatch + internal tools
- **Scale**: Massive distributed systems
- **Key Practices**:
  - Per-service log streams
  - Automated incident response
  - Cost optimization through log sampling

#### **Microsoft**
- **Strategy**: Unified observability platform
- **Tools**: Azure Monitor + Application Insights
- **Scale**: Global cloud infrastructure
- **Key Practices**:
  - Correlation IDs across services
  - Intelligent alerting
  - Predictive analytics

### **Common Enterprise Patterns:**

#### **Log Levels and Sampling**:
```go
// Production: Only INFO and above
// Staging: DEBUG and above with sampling
// Development: ALL levels

func setupLogging(env string) {
    switch env {
    case "production":
        // Sample DEBUG logs (1 in 100)
        setupSampledLogging(0.01)
    case "staging":
        setupLogging(slog.LevelDebug)
    default:
        setupLogging(slog.LevelDebug)
    }
}
```

#### **Cost Management**:
- Log retention policies (7-30-90 days)
- Different storage tiers (hot/warm/cold)
- Sampling for high-volume logs
- Compression and archival

#### **Security and Compliance**:
- Log encryption at rest and in transit
- Access controls and audit trails
- Data residency requirements
- GDPR compliance (log anonymization)

### **Real-World Implementation Example:**

```go
package main

import (
    "context"
    "log/slog"
    "net/http"
    "time"
    
    "github.com/google/uuid"
)

type Logger struct {
    *slog.Logger
    service string
    version string
    env     string
}

func NewLogger(service, version, env string) *Logger {
    handler := slog.NewJSONHandler(os.Stdout, &slog.HandlerOptions{
        Level: getLogLevel(env),
    })
    
    logger := slog.New(handler).With(
        "service", service,
        "version", version,
        "environment", env,
    )
    
    return &Logger{
        Logger:  logger,
        service: service,
        version: version,
        env:     env,
    }
}

func (l *Logger) LogHTTPRequest(r *http.Request, status int, duration time.Duration) {
    l.Info("HTTP request processed",
        "method", r.Method,
        "path", r.URL.Path,
        "status", status,
        "duration_ms", duration.Milliseconds(),
        "user_agent", r.UserAgent(),
        "request_id", r.Header.Get("X-Request-ID"),
        "remote_addr", r.RemoteAddr,
    )
}

func (l *Logger) LogBusinessEvent(event string, userID string, metadata map[string]interface{}) {
    args := []interface{}{
        "event_type", "business",
        "event_name", event,
        "user_id", userID,
        "timestamp", time.Now().Unix(),
    }
    
    for k, v := range metadata {
        args = append(args, k, v)
    }
    
    l.Info("Business event", args...)
}

func (l *Logger) LogError(err error, context map[string]interface{}) {
    args := []interface{}{
        "error", err.Error(),
        "error_type", "application_error",
    }
    
    for k, v := range context {
        args = append(args, k, v)
    }
    
    l.Error("Application error occurred", args...)
}

// Middleware for request logging
func (l *Logger) HTTPMiddleware(next http.Handler) http.Handler {
    return http.HandlerFunc(func(w http.ResponseWriter, r *http.Request) {
        start := time.Now()
        
        // Add request ID if not present
        requestID := r.Header.Get("X-Request-ID")
        if requestID == "" {
            requestID = uuid.New().String()
            r.Header.Set("X-Request-ID", requestID)
        }
        
        // Add request ID to context
        ctx := context.WithValue(r.Context(), "request_id", requestID)
        r = r.WithContext(ctx)
        
        // Wrap response writer to capture status
        wrapped := &responseWriter{ResponseWriter: w, status: 200}
        
        next.ServeHTTP(wrapped, r)
        
        duration := time.Since(start)
        l.LogHTTPRequest(r, wrapped.status, duration)
    })
}

type responseWriter struct {
    http.ResponseWriter
    status int
}

func (rw *responseWriter) WriteHeader(code int) {
    rw.status = code
    rw.ResponseWriter.WriteHeader(code)
}

func getLogLevel(env string) slog.Level {
    switch env {
    case "production":
        return slog.LevelInfo
    case "staging":
        return slog.LevelDebug
    default:
        return slog.LevelDebug
    }
}
```

## Best Practices Summary:

1. **Always use structured logging** (JSON format)
2. **Include correlation IDs** for request tracing
3. **Log at appropriate levels** (INFO for business events, ERROR for failures)
4. **Never log sensitive information**
5. **Use consistent field names** across services
6. **Implement log sampling** for high-volume applications
7. **Set up proper alerting** on error patterns
8. **Plan for log retention** and storage costs
9. **Test your logging** in development environments
10. **Monitor your monitoring** - ensure log collectors are working

This comprehensive approach will give you enterprise-grade logging that integrates seamlessly with monitoring tools and provides the observability needed for modern applications.

```go
package main

import (
	"bytes"
	"context"
	"encoding/json"
	"fmt"
	"io"
	"log/slog"
	"net/http"
	"os"
	"strconv"
	"strings"
	"time"

	"github.com/google/uuid"
)

// Config holds logging configuration
type Config struct {
	Environment    string
	LogLevel       string
	LogPayload     bool
	ServiceName    string
	ServiceVersion string
}

// Logger wraps slog with additional functionality
type Logger struct {
	*slog.Logger
	config *Config
}

// loadConfig loads configuration from environment variables
func loadConfig() *Config {
	config := &Config{
		Environment:    getEnv("APP_ENV", "development"),
		LogLevel:       getEnv("LOG_LEVEL", "info"),
		LogPayload:     getEnvBool("LOG_PAYLOAD", false),
		ServiceName:    getEnv("SERVICE_NAME", "rest-api"),
		ServiceVersion: getEnv("SERVICE_VERSION", "1.0.0"),
	}
	return config
}

func getEnv(key, defaultValue string) string {
	if value := os.Getenv(key); value != "" {
		return value
	}
	return defaultValue
}

func getEnvBool(key string, defaultValue bool) bool {
	if value := os.Getenv(key); value != "" {
		if parsed, err := strconv.ParseBool(value); err == nil {
			return parsed
		}
	}
	return defaultValue
}

// NewLogger creates a new logger with environment-based configuration
func NewLogger(config *Config) *Logger {
	var handler slog.Handler

	// Configure handler based on environment
	opts := &slog.HandlerOptions{
		Level:     parseLogLevel(config.LogLevel),
		AddSource: config.Environment == "development",
	}

	if config.Environment == "production" {
		// JSON format for production (machine readable)
		handler = slog.NewJSONHandler(os.Stdout, opts)
	} else {
		// Text format for development (human readable)
		handler = slog.NewTextHandler(os.Stdout, opts)
	}

	// Add service-level attributes
	logger := slog.New(handler).With(
		"service", config.ServiceName,
		"version", config.ServiceVersion,
		"environment", config.Environment,
	)

	return &Logger{
		Logger: logger,
		config: config,
	}
}

// parseLogLevel converts string to slog.Level
func parseLogLevel(level string) slog.Level {
	switch strings.ToLower(level) {
	case "debug":
		return slog.LevelDebug
	case "info":
		return slog.LevelInfo
	case "warn", "warning":
		return slog.LevelWarn
	case "error":
		return slog.LevelError
	default:
		return slog.LevelInfo
	}
}

// responseWriter captures response details
type responseWriter struct {
	http.ResponseWriter
	statusCode int
	body       *bytes.Buffer
}

func newResponseWriter(w http.ResponseWriter, captureBody bool) *responseWriter {
	var body *bytes.Buffer
	if captureBody {
		body = &bytes.Buffer{}
	}
	return &responseWriter{
		ResponseWriter: w,
		statusCode:     http.StatusOK,
		body:          body,
	}
}

func (rw *responseWriter) WriteHeader(code int) {
	rw.statusCode = code
	rw.ResponseWriter.WriteHeader(code)
}

func (rw *responseWriter) Write(data []byte) (int, error) {
	if rw.body != nil {
		rw.body.Write(data)
	}
	return rw.ResponseWriter.Write(data)
}

// LoggingMiddleware provides HTTP request/response logging
func (l *Logger) LoggingMiddleware(next http.Handler) http.Handler {
	return http.HandlerFunc(func(w http.ResponseWriter, r *http.Request) {
		start := time.Now()

		// Generate or extract request ID
		requestID := r.Header.Get("X-Request-ID")
		if requestID == "" {
			requestID = uuid.New().String()
			r.Header.Set("X-Request-ID", requestID)
		}

		// Add request ID to context
		ctx := context.WithValue(r.Context(), "request_id", requestID)
		r = r.WithContext(ctx)

		// Read and restore request body if payload logging is enabled
		var requestBody []byte
		var requestPayload interface{}
		
		if l.config.LogPayload && r.Body != nil && r.ContentLength > 0 {
			requestBody, _ = io.ReadAll(r.Body)
			r.Body = io.NopCloser(bytes.NewBuffer(requestBody))
			
			// Try to parse as JSON for structured logging
			if strings.Contains(r.Header.Get("Content-Type"), "application/json") {
				json.Unmarshal(requestBody, &requestPayload)
			}
		}

		// Wrap response writer to capture response
		captureResponseBody := l.config.LogPayload && l.config.Environment != "production"
		rw := newResponseWriter(w, captureResponseBody)

		// Process request
		next.ServeHTTP(rw, r)

		// Calculate duration
		duration := time.Since(start)

		// Build log attributes
		attrs := []slog.Attr{
			slog.String("request_id", requestID),
			slog.String("method", r.Method),
			slog.String("path", r.URL.Path),
			slog.String("query", r.URL.RawQuery),
			slog.Int("status", rw.statusCode),
			slog.Duration("duration", duration),
			slog.String("user_agent", r.UserAgent()),
			slog.String("remote_addr", r.RemoteAddr),
			slog.Int64("content_length", r.ContentLength),
		}

		// Add request payload if enabled
		if l.config.LogPayload && requestPayload != nil {
			attrs = append(attrs, slog.Any("request_payload", requestPayload))
		} else if l.config.LogPayload && len(requestBody) > 0 {
			// Log as string if not JSON
			attrs = append(attrs, slog.String("request_body", string(requestBody)))
		}

		// Add response payload if enabled (only in non-production)
		if captureResponseBody && rw.body != nil && rw.body.Len() > 0 {
			var responsePayload interface{}
			if json.Unmarshal(rw.body.Bytes(), &responsePayload) == nil {
				attrs = append(attrs, slog.Any("response_payload", responsePayload))
			} else {
				attrs = append(attrs, slog.String("response_body", rw.body.String()))
			}
		}

		// Log with appropriate level based on status code
		message := fmt.Sprintf("%s %s", r.Method, r.URL.Path)
		
		if rw.statusCode >= 500 {
			l.Logger.LogAttrs(ctx, slog.LevelError, message, attrs...)
		} else if rw.statusCode >= 400 {
			l.Logger.LogAttrs(ctx, slog.LevelWarn, message, attrs...)
		} else {
			l.Logger.LogAttrs(ctx, slog.LevelInfo, message, attrs...)
		}
	})
}

// StructuredLog provides methods for application logging
func (l *Logger) LogBusinessEvent(ctx context.Context, event string, data map[string]interface{}) {
	attrs := []slog.Attr{
		slog.String("event_type", "business"),
		slog.String("event_name", event),
		slog.Time("timestamp", time.Now()),
	}

	// Add request ID if available
	if requestID, ok := ctx.Value("request_id").(string); ok {
		attrs = append(attrs, slog.String("request_id", requestID))
	}

	// Add custom data
	for key, value := range data {
		attrs = append(attrs, slog.Any(key, value))
	}

	l.Logger.LogAttrs(ctx, slog.LevelInfo, "Business event", attrs...)
}

func (l *Logger) LogError(ctx context.Context, err error, message string, data map[string]interface{}) {
	attrs := []slog.Attr{
		slog.String("error", err.Error()),
		slog.String("error_type", "application_error"),
	}

	// Add request ID if available
	if requestID, ok := ctx.Value("request_id").(string); ok {
		attrs = append(attrs, slog.String("request_id", requestID))
	}

	// Add custom data
	for key, value := range data {
		attrs = append(attrs, slog.Any(key, value))
	}

	l.Logger.LogAttrs(ctx, slog.LevelError, message, attrs...)
}

// Example handlers
func (l *Logger) createUserHandler(w http.ResponseWriter, r *http.Request) {
	ctx := r.Context()
	
	var user struct {
		Name  string `json:"name"`
		Email string `json:"email"`
	}

	if err := json.NewDecoder(r.Body).Decode(&user); err != nil {
		l.LogError(ctx, err, "Failed to decode user data", map[string]interface{}{
			"endpoint": "/users",
		})
		http.Error(w, "Invalid JSON", http.StatusBadRequest)
		return
	}

	// Simulate business logic
	userID := uuid.New().String()
	
	// Log business event
	l.LogBusinessEvent(ctx, "user_created", map[string]interface{}{
		"user_id": userID,
		"name":    user.Name,
		"email":   user.Email,
	})

	response := map[string]interface{}{
		"id":    userID,
		"name":  user.Name,
		"email": user.Email,
	}

	w.Header().Set("Content-Type", "application/json")
	json.NewEncoder(w).Encode(response)
}

func (l *Logger) getUserHandler(w http.ResponseWriter, r *http.Request) {
	ctx := r.Context()
	userID := r.URL.Query().Get("id")

	if userID == "" {
		l.LogError(ctx, fmt.Errorf("missing user ID"), "User ID not provided", map[string]interface{}{
			"endpoint": "/users",
		})
		http.Error(w, "User ID required", http.StatusBadRequest)
		return
	}

	// Simulate user lookup
	user := map[string]interface{}{
		"id":    userID,
		"name":  "John Doe",
		"email": "john@example.com",
	}

	l.LogBusinessEvent(ctx, "user_retrieved", map[string]interface{}{
		"user_id": userID,
	})

	w.Header().Set("Content-Type", "application/json")
	json.NewEncoder(w).Encode(user)
}

func main() {
	// Load configuration
	config := loadConfig()
	
	// Initialize logger
	logger := NewLogger(config)
	
	// Log startup information
	logger.Info("Starting REST API server",
		"port", 8080,
		"log_level", config.LogLevel,
		"log_payload", config.LogPayload,
	)

	// Setup routes with logging middleware
	mux := http.NewServeMux()
	
	// Add your handlers
	mux.HandleFunc("POST /users", logger.createUserHandler)
	mux.HandleFunc("GET /users", logger.getUserHandler)
	
	// Health check endpoint
	mux.HandleFunc("GET /health", func(w http.ResponseWriter, r *http.Request) {
		w.Header().Set("Content-Type", "application/json")
		json.NewEncoder(w).Encode(map[string]string{"status": "healthy"})
	})

	// Wrap with logging middleware
	handler := logger.LoggingMiddleware(mux)

	// Start server
	server := &http.Server{
		Addr:    ":8080",
		Handler: handler,
	}

	logger.Info("Server listening on :8080")
	if err := server.ListenAndServe(); err != nil {
		logger.Error("Server failed to start", "error", err)
	}
}
```