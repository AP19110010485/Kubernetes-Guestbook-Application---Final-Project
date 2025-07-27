# Stage 1: Build the Go binary
# This stage is named 'builder' and uses a Golang image (version 1.18)
# It's responsible for compiling the Go application.
FROM golang:1.18 AS builder

# Set the working directory inside the container for this stage.
# All subsequent commands will be executed relative to this directory.
WORKDIR /app

# Copy the main Go source file into the container's /app directory.
COPY main.go .

# Initialize the Go module for dependency management.
# This creates a go.mod file if it doesn't exist.
RUN go mod init guestbook

# Tidy up Go module dependencies.
# This adds missing modules and removes unused ones, ensuring a clean dependency graph.
RUN go mod tidy

# Build the Go application.
# The '-o main' flag specifies the output executable name as 'main'.
RUN go build -o main main.go

# Stage 2: Create the final runtime container
# This stage uses a lighter Ubuntu image (version 18.04) for the final application.
# This multi-stage build helps to keep the final image size small by not including build tools.
FROM ubuntu:18.04

# Copy the compiled Go binary from the 'builder' stage into the final image.
# The binary is renamed to 'guestbook' for clarity.
COPY --from=builder /app/main /app/guestbook

# Copy static files (HTML, CSS, JS, jQuery) into the final image.
# These are the front-end assets required by the web application.
COPY public/index.html /app/public/index.html
COPY public/script.js /app/public/script.js
COPY public/style.css /app/public/style.css
COPY public/jquery.min.js /app/public/jquery.min.js

# Set the working directory for the final container.
# This is where the application will be run from.
WORKDIR /app

# Expose port 3000 to the outside world.
# This informs Docker that the container listens on this port at runtime.
EXPOSE 3000

# Define the command to run the application when the container starts.
# This executes the compiled 'guestbook' binary.
CMD ["./guestbook"]
