---
title: Resources
weight: 70
cSpell:ignore: sdktrace thirdparty
---

{{% docs/languages/resources-intro %}}

Resources should be assigned to a tracer provider at its initialization, and are
created much like attributes:

```go
res := resource.NewWithAttributes(
    semconv.SchemaURL,
    semconv.ServiceNameKey.String("myService"),
    semconv.ServiceVersionKey.String("1.0.0"),
    semconv.ServiceInstanceIDKey.String("abcdef12345"),
)

provider := sdktrace.NewTracerProvider(
    ...
    sdktrace.WithResource(res),
)
```

Note the use of the `semconv` package to provide
[conventional names](/docs/concepts/semantic-conventions/) for resource
attributes. This helps ensure that consumers of telemetry produced with these
semantic conventions can easily discover relevant attributes and understand
their meaning.

Resources can also be detected automatically through `resource.Detector`
implementations. These `Detector`s may discover information about the currently
running process, the operating system it is running on, the cloud provider
hosting that operating system instance, or any number of other resource
attributes.

```go
res, err := resource.New(context.Background(),
	resource.WithFromEnv(),      // Pull attributes from OTEL_RESOURCE_ATTRIBUTES and OTEL_SERVICE_NAME environment variables.
	resource.WithTelemetrySDK(), // Provide information about the OpenTelemetry SDK used.
	resource.WithProcess(),      // Discover and provide process information.
	resource.WithOS(),           // Discover and provide OS information.
	resource.WithContainer(),    // Discover and provide container information.
	resource.WithHost(),         // Discover and provide information.
	resource.WithAttributes(attribute.String("foo", "bar")), // Add custom resource attributes.
	// resource.WithDetectors(thirdparty.Detector{}), // Bring your own external Detector implementation.
)
if err != nil {
	log.Println(err) // Log issues during resource creation. Note that resource.New still returns a resource.
}
```
