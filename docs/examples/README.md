# Docker Multi-Architecture Examples

This directory contains example Dockerfiles and scripts demonstrating best practices for building Docker images that support multiple architectures.

## Files

### Dockerfile.multi-arch

A complete example showing how to properly handle architecture-specific package installations in a multi-platform Docker build. This example specifically addresses the common issue of hardcoded architecture URLs that cause builds to fail on non-amd64 platforms.

**Key Features:**
- Uses Docker BuildKit's automatic platform arguments (`TARGETPLATFORM`, `TARGETARCH`)
- Demonstrates runtime architecture detection with `uname -m`
- Shows proper error handling for unsupported architectures
- Includes real-world examples (AWS CLI, AWS Session Manager Plugin)

**Usage:**

```bash
# Build for multiple platforms
docker buildx build --platform linux/amd64,linux/arm64 \
  -f Dockerfile.multi-arch \
  -t example:latest .

# Build for a specific platform
docker buildx build --platform linux/arm64 \
  -f Dockerfile.multi-arch \
  -t example:arm64 \
  --load .
```

## Related Documentation

See [../docker-multi-arch-builds.md](../docker-multi-arch-builds.md) for comprehensive documentation on multi-architecture Docker builds, including:
- Common pitfalls and solutions
- Best practices
- Architecture mapping tables
- Testing strategies

## Background

These examples were created in response to real-world build failures where Docker images failed to build for ARM64 architecture due to hardcoded AMD64 package URLs. The specific issue that motivated these examples is documented at:

https://github.com/xpipe-io/xpipe-webtop/actions/runs/20578223451/job/59099982309

## Testing

To test these examples locally:

1. Ensure Docker BuildKit is enabled:
   ```bash
   export DOCKER_BUILDKIT=1
   ```

2. Set up buildx if not already configured:
   ```bash
   docker buildx create --use
   ```

3. Build and test:
   ```bash
   # Test AMD64 build
   docker buildx build --platform linux/amd64 \
     -f Dockerfile.multi-arch \
     -t test:amd64 \
     --load .
   
   # Test ARM64 build
   docker buildx build --platform linux/arm64 \
     -f Dockerfile.multi-arch \
     -t test:arm64 \
     --load .
   ```

## Contributing

When adding new examples:
1. Ensure they demonstrate real-world scenarios
2. Include comprehensive comments
3. Test on both amd64 and arm64 platforms
4. Update this README with usage instructions
