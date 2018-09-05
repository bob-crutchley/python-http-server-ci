# docker-jenkins
Automated Jenkins server setup using Docker

to run the jenkins server:
```bash
docker run -d -p 8080:8080 bobcrutchley/jenkins:latest
```

the docker image can be built locally using this command:
```bash
docker build -t bobcrutchley/jenkins:latest .
```

