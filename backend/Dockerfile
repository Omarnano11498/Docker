# ==========================================
# STAGE 1: Build Stage
# ==========================================

# Use the official Go 1.18 Alpine image as the base for building, aliased as "builder"
FROM golang:1.18-alpine AS builder

# Install 'git' without storing local package cache (needed if dependencies pull directly from Git repositories)
RUN apk add --no-cache git 

# Set '/app' as the current working directory inside the container for all following commands
WORKDIR /app  

# Copy dependency definition files (go.mod and go.sum) into the working directory (/app)
COPY go.mod go.sum ./ 

# Download all Go modules listed in go.mod (cached as a Docker layer until go.mod/go.sum changes)
RUN go mod download 

# Install 'curl' without caching package indexes (useful for network scripts or health checks during build)
RUN apk add --no-cache curl 

# Copy all remaining source code from the host machine into the container's working directory (/app)
COPY . .  

# Compile the Go app into a static Linux binary named 'server' placed at root (/server)
# CGO_ENABLED=0 disables C bindings to ensure a statically linked binary that runs on minimal Linux
RUN CGO_ENABLED=0 GOOS=linux go build -o /server main.go 

# ==========================================
# STAGE 2: Final (Runtime) Stage
# ==========================================

# Start a fresh, minimal runtime stage using the latest Alpine Linux base image
FROM alpine:latest  

# Install essential runtime tools: 'curl' for diagnostics/health checks and 'ca-certificates' for outbound HTTPS connections
RUN apk add --no-cache \
    curl \
    ca-certificates 

# Set the working directory in the final container to '/root/'
WORKDIR /root/  

# Copy only the compiled binary '/server' from the 'builder' stage into the current working directory (/root/)
COPY --from=builder /server .

# Inform Docker that the containerized application listens on TCP port 8000 at runtime
EXPOSE 8000  

# Define the default execution command to run when the container starts
CMD ["./server"]
