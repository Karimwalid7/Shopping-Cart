# -------- Stage 1: Extract layers from Spring Boot JAR --------
FROM eclipse-temurin:8-jre-alpine AS builder

WORKDIR /app

# Copy the built jar
COPY target/shopping-cart-0.0.1-SNAPSHOT.jar app.jar

# Extract layers (requires Spring Boot layered jar enabled)
RUN java -Djarmode=layertools -jar app.jar extract


# -------- Stage 2: Final lightweight runtime image --------
FROM eclipse-temurin:8-jre-alpine

# Set working directory
WORKDIR /app

# Copy layers in correct order (maximizes caching)
COPY --from=builder /app/dependencies/ ./
COPY --from=builder /app/spring-boot-loader/ ./
COPY --from=builder /app/snapshot-dependencies/ ./
COPY --from=builder /app/application/ ./

# Expose app port
EXPOSE 8070

# JVM options (optional, can override at runtime)
ENV JAVA_OPTS="-Xms128m -Xmx512m -Djava.security.egd=file:/dev/./urandom -Djava.io.tmpdir=/tmp"

# Run the app
ENTRYPOINT ["sh", "-c", "java $JAVA_OPTS org.springframework.boot.loader.JarLauncher"]
