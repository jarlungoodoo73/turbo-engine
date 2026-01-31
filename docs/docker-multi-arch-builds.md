# Docker Multi-Architecture Build Issues

## Overview

This documentation addresses common issues when building Docker images for multiple architectures. While the GitHub CLI repository itself doesn't require Docker multi-architecture builds, this guide serves as a reference for developers who may containerize GitHub CLI or face similar architecture-specific issues in their own projects.

## Problem Overview

When building Docker images for multiple architectures (e.g., `linux/amd64` and `linux/arm64`), architecture-specific binary downloads must be handled correctly. A common mistake is hardcoding the architecture in download URLs, which causes builds to fail when targeting different platforms.

## Example Issue

Reference: https://github.com/xpipe-io/xpipe-webtop/actions/runs/20578223451/job/59099982309

The following Dockerfile snippet demonstrates the problem:

```dockerfile
RUN echo "**** aws ssm ****" && \
    curl "https://s3.amazonaws.com/session-manager-downloads/plugin/latest/ubuntu_64bit/session-manager-plugin.deb" \
    -o "/tmp/session-manager-plugin.deb" && \
    sudo dpkg -i "/tmp/session-manager-plugin.deb"
```

### Error Message

```
dpkg: error processing archive /tmp/session-manager-plugin.deb (--install):
 package architecture (amd64) does not match system (arm64)
```

The URL hardcodes `ubuntu_64bit` (amd64), but the Docker build is targeting `linux/arm64`, causing an architecture mismatch.

## Solution

Make the download URL architecture-aware by detecting the target platform and selecting the appropriate binary:

```dockerfile
RUN echo "**** aws ssm ****" && \
    ARCH=$(uname -m) && \
    case "$ARCH" in \
        x86_64) \
            SSM_URL="https://s3.amazonaws.com/session-manager-downloads/plugin/latest/ubuntu_64bit/session-manager-plugin.deb" \
            ;; \
        aarch64) \
            SSM_URL="https://s3.amazonaws.com/session-manager-downloads/plugin/latest/ubuntu_arm64/session-manager-plugin.deb" \
            ;; \
        *) \
            echo "Unsupported architecture: $ARCH" && exit 1 \
            ;; \
    esac && \
    curl "$SSM_URL" -o "/tmp/session-manager-plugin.deb" && \
    sudo dpkg -i "/tmp/session-manager-plugin.deb" && \
    rm -f "/tmp/session-manager-plugin.deb"
```

### Key Changes

1. **Detect Architecture**: Use `uname -m` to get the current architecture
2. **Map Architecture**: Convert the architecture to the correct package variant:
   - `x86_64` → `ubuntu_64bit`
   - `aarch64` → `ubuntu_arm64`
3. **Dynamic URL**: Construct the download URL based on detected architecture
4. **Error Handling**: Fail explicitly for unsupported architectures

## Alternative Approach Using TARGETPLATFORM

Docker BuildKit provides build arguments that can be used to detect the target platform. These must be declared with ARG statements:

```dockerfile
ARG TARGETPLATFORM
ARG TARGETOS
ARG TARGETARCH

RUN echo "**** aws ssm ****" && \
    case "$TARGETPLATFORM" in \
        linux/amd64) \
            SSM_PKG="ubuntu_64bit" \
            ;; \
        linux/arm64) \
            SSM_PKG="ubuntu_arm64" \
            ;; \
        *) \
            echo "Unsupported platform: $TARGETPLATFORM" && exit 1 \
            ;; \
    esac && \
    curl "https://s3.amazonaws.com/session-manager-downloads/plugin/latest/${SSM_PKG}/session-manager-plugin.deb" \
    -o "/tmp/session-manager-plugin.deb" && \
    sudo dpkg -i "/tmp/session-manager-plugin.deb" && \
    rm -f "/tmp/session-manager-plugin.deb"
```

## Best Practices

1. **Always Check Architecture**: Never hardcode architecture-specific URLs
2. **Test Multi-Platform Builds**: Use `docker buildx build --platform linux/amd64,linux/arm64`
3. **Handle Unsupported Architectures**: Fail gracefully with clear error messages
4. **Use BuildKit Arguments**: Leverage `TARGETPLATFORM`, `TARGETOS`, `TARGETARCH` when available
5. **Document Platform Support**: Clearly document which architectures your image supports

## Testing

To test multi-architecture builds locally:

```bash
# Enable BuildKit
export DOCKER_BUILDKIT=1

# Build for multiple platforms
docker buildx build \
  --platform linux/amd64,linux/arm64 \
  -t myimage:latest \
  .

# Or build and test a specific platform
docker buildx build \
  --platform linux/arm64 \
  -t myimage:arm64 \
  --load \
  .
```

## Common Architecture Mappings

| `uname -m` | Docker Platform | Debian/Ubuntu Package |
|------------|-----------------|----------------------|
| x86_64     | linux/amd64     | amd64               |
| aarch64    | linux/arm64     | arm64               |
| armv7l     | linux/arm/v7    | armhf               |
| i386       | linux/386       | i386                |

## Additional Resources

- [Docker BuildKit Multi-Platform Images](https://docs.docker.com/build/building/multi-platform/)
- [Docker BuildKit Automatic Platform ARGs](https://docs.docker.com/engine/reference/builder/#automatic-platform-args-in-the-global-scope)
- [AWS Session Manager Plugin Installation](https://docs.aws.amazon.com/systems-manager/latest/userguide/session-manager-working-with-install-plugin.html)
