A GitHub Actions egy folyamatos integrációt és folyamatos szállítást (CI/CD) biztosító platform, amely közvetlenül a GitHub adattárakba integrálódik. Lehetővé teszi a fejlesztők számára a szoftver munkafolyamatok automatizálását, beleértve az alkalmazások létrehozását, tesztelését és telepítését. A műveletek YAML fájlokban vannak definiálva, és különféle GitHub események, például push-ok, pull-requestek vagy ütemezett feladatok indítják el őket. A platform előre elkészített műveletek piacterét biztosítja, és támogatja az egyéni műveleteket. A GitHub Actions mátrix-buildeket, párhuzamos feladatvégrehajtást kínál, és több operációs rendszert és nyelvet is támogat. Zökkenőmentesen integrálódik a GitHub ökoszisztémájába, megkönnyítve az automatizált kódfelülvizsgálatot, a problémakövetést és a projektmenedzsmentet. Ez az eszköz lehetővé teszi a fejlesztők számára, hogy hatékonyan alkalmazzák a DevOps gyakorlatokat a GitHub munkafolyamataikban, növelve a termelékenységet és a kódminőséget.

# HProfile Microservice – CI/CD + AWS Deployment

Ez a projekt egy Spring Boot alapú microservice, amely teljes CI/CD folyamaton megy keresztül GitHub Actions segítségével, és AWS felhőbe van deploy-olva (ECR + ECS + ELB + RDS).

# Projekt célja

A cél egy valós ipari környezetet szimuláló fejlesztési és üzemeltetési pipeline létrehozása, amely lefedi:

- Tesztelés (JUnit, Checkstyle, SonarQube)
- Docker image építés és publikálás ECR-re
- ECS cluster-en történő automatikus deploy

# Használt technológiák

- Java 11 / Maven / Spring Boot
- SonarQube – statikus kódelemzés
- GitHub Actions – CI/CD pipeline
- Docker – image build és ECR-re push
- AWS:
  - ECR (Elastic Container Registry)
  - ECS (Elastic Container Service)
  - ELB (Load Balancer)
  - RDS (Relational Database Service – MySQL)
  - IAM + Security Groups

# GitHub Actions workflow

A main.yml fájl három fő job-ból áll:

# 1. Testing

- mvn test – egységtesztek futtatása
- mvn checkstyle:checkstyle – kódstílus ellenőrzés
- sonar-scanner – kódminőség vizsgálat

# 2. BUILD_AND_PUBLISH

- Dockerfile alapján image build
- application.properties frissítése titkos értékekkel (RDS credentials, endpoint)
- Image push az AWS ECR-re

# 3. Deploy

- Task definition JSON alapján image frissítése
- Deploy a megadott ECS service-be
- wait-for-service-stability: true biztosítja a sikeres újraindítást

# AWS Setup (röviden)

> Részletes lépésről-lépés útmutatót a githubactions.txt dokumentációjában találsz.

- ECR repo: actapp
- ECS cluster: vproapp-act
- Task definition: vproapp-act91-tdef
- Service: vproapp-act-svc
- Load balancer: vproapp-act-elb
- RDS endpoint: titkosítva secrets.RDS_ENDPOINT változóban

# Secrets (GitHub repository → Settings → Secrets)

- AWS_ACCESS_KEY_ID
- AWS_SECRET_ACCESS_KEY
- REGISTRY → ECR registry URL (pl. 1234567890.dkr.ecr.us-east-1.amazonaws.com)
- RDS_USER, RDS_PASS, RDS_ENDPOINT
- SONAR_TOKEN, SONAR_URL, SONAR_ORGANIZATION, SONAR_PROJECT_KEY

# Futás & Deploy

# Manuális futtatás:

1. Lépj be a GitHub repository-ba
2. Menj az Actions fülre
3. Válaszd ki a Hprofile Actions workflow-t
4. Kattints: "Run workflow"

# Automatikus futás:

main.yml -> "on: [push, workflow_dispatch]"

A workflow automatikusan lefut, ha:

- Commit érkezik a main ágra
