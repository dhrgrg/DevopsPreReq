# DevOps Guide: Running a Java Backend on a Fresh Ubuntu Machine

## Scenario

You have:

- A fresh Ubuntu machine
- No Java installed
- No Maven or Gradle installed
- Only the application's source code
- Need to verify the application before containerizing it

---

# Step 1: Get the Source Code

Clone the repository.

```bash
git clone https://github.com/company/backend.git
```

Move into the project.

```bash
cd backend
```

---

# Step 2: Inspect the Project Structure

Run:

```bash
ls -la
```

Typical Spring Boot project:

```text
backend/
│
├── src/
│   ├── main/
│   └── test/
│
├── pom.xml
├── mvnw
├── mvnw.cmd
├── .mvn/
├── application.properties
├── application.yml
└── README.md
```

or Gradle project

```text
backend/
│
├── src/
├── build.gradle
├── settings.gradle
├── gradlew
├── gradlew.bat
└── README.md
```

---

# Step 3: Read README

Always read:

```bash
cat README.md
```

Look for:

- Java version
- Build tool
- Required services
- Environment variables
- Startup instructions

---

# Step 4: Determine Java Version

Look inside:

### pom.xml

```xml
<java.version>21</java.version>
```

or

```xml
<maven.compiler.source>17</maven.compiler.source>
```

or

```text
README.md
```

Never assume.

---

# Step 5: Identify Build Tool

## Maven

Look for:

```text
pom.xml
```

## Gradle

Look for:

```text
build.gradle
```

or

```text
build.gradle.kts
```

---

# Step 6: Install Java

Update Ubuntu.

```bash
sudo apt update
```

Install OpenJDK.

Example Java 21:

```bash
sudo apt install openjdk-21-jdk -y
```

Verify.

```bash
java -version
```

```bash
javac -version
```

Expected:

```text
openjdk version "21"
```

---

# Step 7: Install Build Tool

## Maven

```bash
sudo apt install maven -y
```

Verify.

```bash
mvn -version
```

---

## Gradle

```bash
sudo apt install gradle -y
```

or use project wrapper:

```bash
./gradlew
```

Wrapper is preferred because it uses the project's required Gradle version.

---

# Step 8: Configure Environment Variables

Look for

```text
application.properties

application.yml

.env.example
```

Example:

```properties
server.port=8080

spring.datasource.url=

spring.datasource.username=

spring.datasource.password=
```

---

# Step 9: Identify External Services

Look inside dependencies.

Examples:

| Dependency | Service |
|------------|----------|
| spring-data-jpa | Database |
| mysql-connector | MySQL |
| postgresql | PostgreSQL |
| spring-data-mongodb | MongoDB |
| lettuce | Redis |
| spring-kafka | Kafka |
| rabbitmq | RabbitMQ |

---

# Step 10: Build the Application

## Maven

```bash
mvn clean package
```

or

```bash
./mvnw clean package
```

Preferred:

Use **mvnw** if it exists.

Output:

```text
target/
```

Example:

```text
target/app.jar
```

---

## Gradle

```bash
./gradlew build
```

Output:

```text
build/libs/
```

---

# Step 11: Run Unit Tests

Maven:

```bash
mvn test
```

Gradle:

```bash
./gradlew test
```

---

# Step 12: Database Migration

Some projects use:

- Flyway
- Liquibase

These usually execute automatically when Spring Boot starts.

---

# Step 13: Run the Application

Spring Boot JAR:

```bash
java -jar target/app.jar
```

Gradle:

```bash
./gradlew bootRun
```

Maven:

```bash
mvn spring-boot:run
```

---

# Step 14: Verify the Application

Check processes.

```bash
ps -ef | grep java
```

Check listening ports.

```bash
ss -tulpn
```

Typical port:

```text
8080
```

---

# Step 15: Test Endpoints

```bash
curl http://localhost:8080
```

Health endpoint:

```bash
curl http://localhost:8080/actuator/health
```

Expected:

```json
{
  "status":"UP"
}
```

(Spring Boot Actuator must be enabled.)

---

# Step 16: Check Logs

Spring Boot logs appear in terminal.

Typical messages:

```text
Tomcat started on port 8080

Started Application in 5.4 seconds
```

---

# Step 17: Troubleshooting

## Java Not Found

```text
java: command not found
```

Install JDK.

---

## Wrong Java Version

Example:

```text
UnsupportedClassVersionError
```

Install the required JDK version.

---

## Maven Dependency Failure

```text
Could not resolve dependencies
```

Check:

- Internet connectivity
- Maven Central access
- Corporate proxy
- Repository configuration

---

## Port Already Used

```bash
sudo lsof -i :8080
```

Kill process.

```bash
kill -9 PID
```

---

## Database Connection Failed

Check:

- Database running
- Username
- Password
- URL
- Firewall
- Network

---

# Step 18: Containerize

After verification:

```text
Clone Repository
        ↓
Read README
        ↓
Install JDK
        ↓
Install Maven/Gradle
        ↓
Configure application.properties
        ↓
Build Project
        ↓
Run Tests
        ↓
Run JAR
        ↓
Verify Health
        ↓
Dockerize
        ↓
Deploy
```

---

# DevOps Checklist

- [ ] Clone repository
- [ ] Read README
- [ ] Identify Java version
- [ ] Install JDK
- [ ] Identify build tool (Maven/Gradle)
- [ ] Install Maven or use Wrapper
- [ ] Configure `application.properties`
- [ ] Configure database/cache/message broker
- [ ] Build application (`mvn package` / `gradlew build`)
- [ ] Run tests
- [ ] Start application (`java -jar`)
- [ ] Verify logs
- [ ] Verify listening port
- [ ] Test application endpoints
- [ ] Verify health endpoint
- [ ] Proceed to Docker containerization

---

# Important Files to Understand

| File | Purpose |
|------|---------|
| `pom.xml` | Maven project configuration |
| `build.gradle` | Gradle project configuration |
| `mvnw` | Maven Wrapper (preferred over installed Maven) |
| `gradlew` | Gradle Wrapper (preferred over installed Gradle) |
| `application.properties` | Application configuration |
| `application.yml` | YAML configuration |
| `target/` | Maven build output |
| `build/libs/` | Gradle build output |
| `README.md` | Setup instructions |

---

# Common Spring Boot Commands

## Build

```bash
mvn clean package
```

## Run Without Packaging

```bash
mvn spring-boot:run
```

## Run Packaged JAR

```bash
java -jar target/app.jar
```

## Run Tests

```bash
mvn test
```

## Skip Tests During Build

```bash
mvn clean package -DskipTests
```

---

# Java DevOps Workflow

```text
Receive Source Code
        ↓
Read README
        ↓
Identify Java Version
        ↓
Install JDK
        ↓
Identify Maven or Gradle
        ↓
Install Dependencies
        ↓
Configure application.properties
        ↓
Build Application
        ↓
Run Unit Tests
        ↓
Start Application
        ↓
Verify Health Endpoint
        ↓
Containerize
        ↓
Deploy
```
