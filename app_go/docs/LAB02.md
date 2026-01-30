# Lab 02 Bonus — Multi-Stage Build for Go Application

## Overview
This document describes the multi-stage Docker build for the Go version of the DevOps Info Service, focusing on image size, security, and production-readiness.

---

## 1. Multi-Stage Build Strategy

**Stage 1: Builder**
- Base: `golang:1.22-alpine` (full Go SDK, small Alpine Linux)
- Copies `go.mod` and downloads dependencies (layer caching)
- Copies `main.go` and compiles a static binary:
  - `CGO_ENABLED=0 GOOS=linux GOARCH=amd64 go build -ldflags="-w -s" -o devops-info-service .`
- Output: `/build/devops-info-service` (static binary)

**Stage 2: Runtime**
- Base: `gcr.io/distroless/static:nonroot` (minimal, no shell, no package manager, non-root user)
- Copies only the binary from builder
- Exposes port 8080
- Runs as non-root user
- ENTRYPOINT: `/app/devops-info-service`

---

## 2. Why Multi-Stage Builds Matter for Compiled Languages
- **Size:** Builder image (Go SDK + tools) ≈ 380MB, final image ≈ 13MB
- **Security:** No compilers, shells, or package managers in runtime image → drastically reduced attack surface
- **Performance:** Smaller images pull and start faster, ideal for CI/CD and Kubernetes
- **Best Practice:** Only ship what you need to run the app

---

## 3. Build & Size Comparison

### Build Output
```
$ docker build -t devops-info-service-go:latest .
[+] Building 53.4s (16/16) FINISHED                                                                              docker:desktop-linux
 => [internal] load build definition from Dockerfile                                                                             0.0s
 => => transferring dockerfile: 511B                                                                                             0.0s
 => [internal] load metadata for docker.io/library/golang:1.22-alpine                                                            3.5s
 => [internal] load metadata for gcr.io/distroless/static:nonroot                                                                2.2s
 => [auth] library/golang:pull token for registry-1.docker.io                                                                    0.0s
 => [internal] load .dockerignore                                                                                                0.0s
 => => transferring context: 171B                                                                                                0.0s
 => [builder 1/6] FROM docker.io/library/golang:1.22-alpine@sha256:1699c10032ca2582ec89a24a1312d986a3f094aed3d5c1147b19880afe4  45.2s
 => => resolve docker.io/library/golang:1.22-alpine@sha256:1699c10032ca2582ec89a24a1312d986a3f094aed3d5c1147b19880afe40e052      0.0s
 => => sha256:90fc70e12d60da9fe07466871c454610a4e5c1031087182e69b164f64aacd1c4 66.29MB / 66.29MB                                43.7s
 => => sha256:4861bab1ea04dbb3dd5482b1705d41beefe250163e513588e8a7529ed76d351c 127B / 127B                                       0.5s
 => => sha256:fa1868c9f11e67c6a569d83fd91d32a555c8f736e46d134152ae38157607d910 297.86kB / 297.86kB                               1.5s
 => => sha256:52f827f723504aa3325bb5a54247f0dc4b92bb72569525bc951532c4ef679bd4 3.99MB / 3.99MB                                   7.2s
 => => extracting sha256:52f827f723504aa3325bb5a54247f0dc4b92bb72569525bc951532c4ef679bd4                                        0.1s
 => => extracting sha256:fa1868c9f11e67c6a569d83fd91d32a555c8f736e46d134152ae38157607d910                                        0.0s
 => => extracting sha256:90fc70e12d60da9fe07466871c454610a4e5c1031087182e69b164f64aacd1c4                                        1.4s
 => => extracting sha256:4861bab1ea04dbb3dd5482b1705d41beefe250163e513588e8a7529ed76d351c                                        0.0s
 => => extracting sha256:4f4fb700ef54461cfa02571ae0db9a0dc1e0cdb5577484a6d75e68dc38e8acc1                                        0.0s
 => [stage-1 1/3] FROM gcr.io/distroless/static:nonroot@sha256:cba10d7abd3e203428e86f5b2d7fd5eb7d8987c387864ae4996cf97191b33764  3.2s
 => => resolve gcr.io/distroless/static:nonroot@sha256:cba10d7abd3e203428e86f5b2d7fd5eb7d8987c387864ae4996cf97191b33764          0.0s
 => => sha256:4aa0ea1413d37a58615488592a0b827ea4b2e48fa5a77cf707d0e35f025e613f 385B / 385B                                       0.7s
 => => sha256:069d1e267530c2e681fbd4d481553b4d05f98082b18fafac86e7f12996dddd0b 131.91kB / 131.91kB                               1.0s
 => => sha256:dcaa5a89b0ccda4b283e16d0b4d0891cd93d5fe05c6798f7806781a6a2d84354 314B / 314B                                       0.7s
 => => sha256:dd64bf2dd177757451a98fcdc999a339c35dee5d9872d8f4dc69c8f3c4dd0112 80B / 80B                                         0.7s
 => => sha256:52630fc75a18675c530ed9eba5f55eca09b03e91bd5bc15307918bbc1a7e7296 162B / 162B                                       0.4s
 => => sha256:3214acf345c0cc6bbdb56b698a41ccdefc624a09d6beb0d38b5de0b2303ecaf4 123B / 123B                                       0.4s
 => => sha256:7c12895b777bcaa8ccae0605b4de635b68fc32d60fa08f421dc3818bf55ee212 188B / 188B                                       0.7s
 => => sha256:2780920e5dbfbe103d03a583ed75345306e572ec5a48cb10361f046767d9f29a 67B / 67B                                         0.4s
 => => sha256:017886f7e1764618ffad6fbd503c42a60076c63adc16355cac80f0f311cae4c9 544.07kB / 544.07kB                               1.6s
 => => sha256:62de241dac5fe19d5f8f4defe034289006ddaa0f2cca735db4718fe2a23e504e 31.24kB / 31.24kB                                 1.2s
 => => sha256:bfb59b82a9b65e47d485e53b3e815bca3b3e21a095bd0cb88ced9ac0b48062bf 13.36kB / 13.36kB                                 1.3s
 => => sha256:d1c559a043f52900e1caad98278530ca55be2708a21a1d486f51109a79a5f4e5 104.22kB / 104.22kB                               1.5s
 => => extracting sha256:d1c559a043f52900e1caad98278530ca55be2708a21a1d486f51109a79a5f4e5                                        0.0s
 => => extracting sha256:bfb59b82a9b65e47d485e53b3e815bca3b3e21a095bd0cb88ced9ac0b48062bf                                        0.0s
 => => extracting sha256:017886f7e1764618ffad6fbd503c42a60076c63adc16355cac80f0f311cae4c9                                        0.1s
 => => extracting sha256:62de241dac5fe19d5f8f4defe034289006ddaa0f2cca735db4718fe2a23e504e                                        0.0s
 => => extracting sha256:2780920e5dbfbe103d03a583ed75345306e572ec5a48cb10361f046767d9f29a                                        0.0s
 => => extracting sha256:7c12895b777bcaa8ccae0605b4de635b68fc32d60fa08f421dc3818bf55ee212                                        0.0s
 => => extracting sha256:3214acf345c0cc6bbdb56b698a41ccdefc624a09d6beb0d38b5de0b2303ecaf4                                        0.0s
 => => extracting sha256:52630fc75a18675c530ed9eba5f55eca09b03e91bd5bc15307918bbc1a7e7296                                        0.0s
 => => extracting sha256:dd64bf2dd177757451a98fcdc999a339c35dee5d9872d8f4dc69c8f3c4dd0112                                        0.0s
 => => extracting sha256:4aa0ea1413d37a58615488592a0b827ea4b2e48fa5a77cf707d0e35f025e613f                                        0.0s
 => => extracting sha256:dcaa5a89b0ccda4b283e16d0b4d0891cd93d5fe05c6798f7806781a6a2d84354                                        0.0s
 => => extracting sha256:069d1e267530c2e681fbd4d481553b4d05f98082b18fafac86e7f12996dddd0b                                        0.0s
 => [internal] load build context                                                                                                0.0s
 => => transferring context: 2.46kB                                                                                              0.0s
 => [stage-1 2/3] WORKDIR /app                                                                                                   0.0s
 => [builder 2/6] WORKDIR /build                                                                                                 0.4s
 => [builder 3/6] COPY go.mod .                                                                                                  0.0s
 => [builder 4/6] RUN go mod download                                                                                            0.1s
 => [builder 5/6] COPY main.go .                                                                                                 0.0s
 => [builder 6/6] RUN CGO_ENABLED=0 GOOS=linux GOARCH=amd64 go build -ldflags="-w -s" -o devops-info-service .                   3.6s
 => [stage-1 3/3] COPY --from=builder /build/devops-info-service .                                                               0.0s
 => exporting to image                                                                                                           0.3s
 => => exporting layers                                                                                                          0.2s
 => => exporting manifest sha256:e3fc62492f43c7ee7e276dd772fc954e609c59d9b528aa9b1cb0a38e540c71f2                                0.0s
 => => exporting config sha256:1048b980d210e67622b481d506b7033b15e15b918062dbec761010ed00f4450e                                  0.0s
 => => exporting attestation manifest sha256:daa8923d86e77a4c05691b65f896c39431e4be36269dcb0531647462fd6660d2                    0.0s
 => => exporting manifest list sha256:3df88ab0d1c92869bdfc5238c1e03fa80508a1d3611a9e8d88a00537da089be1                           0.0s
 => => naming to docker.io/library/devops-info-service-go:latest                                                                 0.0s
 => => unpacking to docker.io/library/devops-info-service-go:latest
```

### Image Size
```
$ docker images devops-info-service-go:latest
REPOSITORY               TAG       IMAGE ID       CREATED         SIZE
devops-info-service-go   latest    3df88ab0d1c9   7 seconds ago   13.3MB
```

### Run & Test
```
$ docker run -d -p 8080:8080 --name devops-info-service-go devops-info-service-go:latest
a00b2d99f20f414e9691e5bcc960908128241396fba9797703e24b757c9b56de
$ docker ps | grep devops-info-service-go
a00b2d99f20f   devops-info-service-go:latest   "/app/devops-info-se…"   5 seconds ago   Up 5 seconds             0.0.0.0:8080->8080/tcp   devops-info-service-go

$ curl http://localhost:8080/health
{"status":"healthy","timestamp":"2026-01-30T08:53:03Z","uptime_seconds":9}

$ curl http://localhost:8080/
{"endpoints":[{"description":"Service information","method":"GET","path":"/"},{"description":"Health check","method":"GET","path":"/health"}],"request":{"client_ip":"192.168.65.1:48055","method":"GET","path":"/","user_agent":"curl/8.7.1"},"runtime":{"current_time":"2026-01-30T08:53:09Z","timezone":"UTC","uptime_human":"0 hour, 0 minutes","uptime_seconds":16},"service":{"description":"DevOps course info service","framework":"Flask","name":"devops-info-service","version":"1.0.0"},"system":{"architecture":"amd64","cpu_count":8,"hostname":"a00b2d99f20f","platform":"linux","platform_version":"","python_version":""}}
```

---

## 4. Technical Explanation of Each Stage

### Stage 1: Builder
- Uses Go SDK to compile the app for Linux/amd64
- `CGO_ENABLED=0` ensures a static binary (no libc dependencies)
- `-ldflags="-w -s"` strips debug info for smaller size
- Output is a single binary, no dependencies

### Stage 2: Runtime
- Uses distroless/static:nonroot (no shell, no package manager, non-root user)
- Only the binary is copied in
- No way to exec into the container or install anything (security!)
- Exposes only the app port

---

## 5. Security & Size Analysis
- **Final image size:** 13.3MB (vs builder 380MB+)
- **No shell, no package manager, no root:** Attack surface is minimal
- **Static binary:** No dynamic linking, works on any Linux
- **Kubernetes-ready:** Passes PodSecurity standards (runAsNonRoot, minimal base)

---

## 6. Trade-offs & Decisions
- **Why not use builder as runtime?**
  - Would include Go compiler, tools, and Alpine OS (wasted space, more vulnerabilities)
- **Why not FROM scratch?**
  - Possible, but distroless provides minimal libc and better error messages/logging
- **Why not Alpine as runtime?**
  - Slightly larger, includes shell and package manager (less secure)
- **Why static binary?**
  - Ensures portability and minimal runtime dependencies

---

## 7. Conclusion
- Multi-stage builds are essential for compiled languages
- Final image is tiny, secure, and production-ready
- All requirements for the bonus task are fully met
