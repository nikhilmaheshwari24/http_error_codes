# HTTP Error Code - 400 - Bad Request

## 🔍 What is HTTP Error Code 400 ?

- Definition: The server cannot process the request because of something perceived as a client error.

- It does not mean server is down or app is crashing.

- Typically returned by web servers, API gateways, or apps that receive HTTP requests.

## 💡 Possible Causes of HTTP 400
| Cause                               | Description                                                                 |
|------------------------------------|-----------------------------------------------------------------------------|
| 🧾 Malformed HTTP request           | Invalid JSON, query string, headers, or body.                              |
| 🧩 Invalid Input Parameters         | Missing required fields or incorrect data types.                           |
| 🔑 Invalid Authentication           | Malformed tokens, incorrect auth headers.                                  |
| 🔀 Incorrect Content-Type           | Example: sending JSON but using Content-Type: text/plain.                  |
| 📦 Payload Too Large                | Hitting request size limits (client_max_body_size, etc.).                  |
| 🚫 Application-level validation     | Custom validation rejecting the request.                                   |
| 🔁 API Gateway or Ingress issues    | Ingress may be misconfigured, or forwarding request incorrectly.           |

## 🧪 How to Debug a 400 Error in Kubernetes

#### Check the Application Logs

```bash
kubectl logs <pod-name> -n <namespace>
```

Look For
- Request validation failures

- Stack traces

- Explicit 400 responses by the app

#### Check Ingress/Nginx Logs (if using Ingress)

```bash
kubectl logs <nginx-pod-name> -n <namespace>
```

Look for:

- Malformed headers

- Rejected request size

- Path mismatches

#### Use Curl or Postman to Reproduce the Error

```bash
curl -v -X POST http://<service-url>/api/endpoint \
  -H "Content-Type: application/json" \
  -d '{ "invalid": "payload" }'
```

- Enable verbose/debug mode to inspect request/response headers.

- Try modifying payloads or headers to isolate the cause.

#### Check the Service & Deployment

```bash
kubectl get svc,deployment,pod -n <namespace> -o wide
kubectl describe svc <service-name> -n <namespace>
```

- Ensure service is correctly routing to the pods

#### Check API Gateway (e.g., Kong, Ambassador, Istio)
If you're using an API gateway:

- Validate route configurations.

- Look for policy mismatches or rejected headers.

#### Enable Debug Logging in the Application (if possible)

- This can help trace the exact request rejection point.


## 🧱 Kubernetes Components to Inspect

| Component                        | What to Check                                               |
|----------------------------------|-------------------------------------------------------------|
| 🔧 **Application Pod**           | Logs, liveness/readiness probes, HTTP server config        |
| 🌐 **Service**                   | Target port, selector labels                                |
| 🌉 **Ingress/Load Balancer**     | URL paths, header rewrites, body size limits               |
| 🕵️‍♀️ **Network Policies**        | Any egress/ingress blocking to app                          |
| 📦 **ConfigMaps / Secrets**      | App settings that control request handling                  |
| 🔒 **Auth Configs (OAuth/JWT/etc.)** | Incorrect or expired tokens in requests                 |

## Checklist to Narrow Down 400 Error

- Can you reproduce the error with curl or Postman?

- Are the request headers and payload valid?

- Do logs in app or ingress show request rejection?

- Are all environment variables and config maps correctly set?

- Is the issue coming from the app or an upstream (Ingress/API Gateway)?