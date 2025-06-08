Voici deux versions complètes d’un pipeline **DevSecOps** intégrant **OWASP** et les meilleures pratiques de sécurité — une pour **Jenkins**, et une pour **GitLab CI** ✅

---

## 🧱 **1. Jenkinsfile – Pipeline DevSecOps (Java/Maven)**
```groovy
pipeline {
  agent any

  environment {
    NVD_API_KEY = credentials('nvd-api-key-id') // stocké dans Jenkins credentials
  }

  tools {
    maven 'Maven 3' // nom défini dans Jenkins > Global Tool Configuration
  }

  stages {
    stage('Checkout') {
      steps {
        checkout scm
      }
    }

    stage('Build & Test') {
      steps {
        sh 'mvn clean install'
      }
    }

    stage('SonarQube Analysis') {
      steps {
        withSonarQubeEnv('Sonar') {
          sh 'mvn sonar:sonar'
        }
      }
    }

    stage('OWASP Dependency Check') {
      steps {
        sh """
          dependency-check.sh \
            --project "my-app" \
            --scan . \
            --format HTML \
            --out dependency-check-report \
            --nvdApiKey ${NVD_API_KEY}
        """
      }
    }

    stage('Container Scan (Trivy)') {
      steps {
        sh 'trivy fs . --severity HIGH,CRITICAL --exit-code 1 --format table'
      }
    }

    stage('Quality Gate') {
      steps {
        timeout(time: 5, unit: 'MINUTES') {
          waitForQualityGate abortPipeline: true
        }
      }
    }

    stage('Publish Reports') {
      steps {
        publishHTML([
          reportDir: 'dependency-check-report',
          reportFiles: 'dependency-check-report.html',
          reportName: 'Dependency Check Report'
        ])
      }
    }
  }
}
```

---

## 🧪 **2. .gitlab-ci.yml – Pipeline DevSecOps pour GitLab**

```yaml
stages:
  - build
  - test
  - security
  - quality

variables:
  MAVEN_OPTS: "-Dmaven.repo.local=.m2/repository"
  NVD_API_KEY: $NVD_API_KEY # Stocké dans GitLab CI/CD > Variables

cache:
  paths:
    - .m2/repository

build:
  stage: build
  script:
    - mvn clean install

sonarqube:
  stage: quality
  script:
    - mvn sonar:sonar
  only:
    - merge_requests
    - main

dependency-check:
  stage: security
  image: owasp/dependency-check
  script:
    - dependency-check.sh --project "my-app" --scan /builds/$CI_PROJECT_PATH --out dependency-report --nvdApiKey $NVD_API_KEY
  artifacts:
    paths:
      - dependency-report

trivy:
  stage: security
  image: aquasec/trivy:latest
  script:
    - trivy fs . --severity HIGH,CRITICAL --exit-code 1 --format table

```

---

## ✅ Recommandations pour les deux :
- Stocker la clé `NVD_API_KEY` dans **les credentials Jenkins** ou **GitLab CI/CD > Variables**
- Ajouter des alertes ou dashboards via DefectDojo, Slack, ou email
- Automatiser la suppression ou le blocage de déploiement si vulnérabilités critiques sont détectées

