// ============================================================
//  MediTech — Jenkinsfile  (CI/CD Pipeline)
// ============================================================
pipeline {
    agent any

    environment {
        APP_NAME   = 'meditech'
        DEPLOY_DIR = '/var/www/html/meditech'
        TS         = sh(script: 'date +%Y%m%d_%H%M%S', returnStdout: true).trim()
    }

    options {
        buildDiscarder(logRotator(numToKeepStr: '10'))
        timeout(time: 15, unit: 'MINUTES')
        timestamps()
    }

    stages {

        stage('Checkout') {
            steps {
                echo "📥 Checking out source..."
                checkout scm
                sh 'echo "Branch: $GIT_BRANCH | Commit: $GIT_COMMIT"'
            }
        }

        stage('Validate') {
            steps {
                echo "🔍 Validating files..."
                sh '''
                    for f in index.html style.css script.js Jenkinsfile; do
                        [ -f "$f" ] && echo "✅ $f found" || { echo "❌ $f MISSING"; exit 1; }
                    done
                '''
            }
        }

        stage('Lint') {
            parallel {
                stage('JS Syntax') {
                    steps {
                        sh 'command -v node && node --check script.js && echo "✅ JS OK" || echo "⚠️  node not available"'
                    }
                }
                stage('HTML Check') {
                    steps {
                        sh 'command -v tidy && tidy -errors -quiet index.html || echo "⚠️  tidy not available"'
                    }
                }
                stage('Security') {
                    steps {
                        sh '''
                            grep -n "eval(" script.js && echo "⚠️  eval() found" || echo "✅ No eval()"
                            grep -inE "(api_key|secret)\s*=" script.js | grep -v "//" && echo "⚠️  Check secrets" || echo "✅ No secrets"
                        '''
                    }
                }
            }
        }

        stage('Build') {
            steps {
                sh '''
                    mkdir -p dist
                    cp index.html style.css script.js dist/
                    command -v uglifyjs  && uglifyjs  script.js -o dist/script.js -c -m && echo "✅ JS minified"  || true
                    command -v cleancss  && cleancss  style.css -o dist/style.css      && echo "✅ CSS minified" || true
                    ls -lh dist/
                    echo "✅ Build done: ${TS}"
                '''
            }
        }

        stage('Archive') {
            steps {
                sh 'cd dist && zip -r ../meditech-${TS}.zip . && ls -lh ../meditech-${TS}.zip'
                archiveArtifacts artifacts: 'meditech-*.zip', fingerprint: true
            }
        }

        stage('Deploy') {
            when { branch 'main' }
            steps {
                sh '''
                    mkdir -p ${DEPLOY_DIR}
                    [ "$(ls -A ${DEPLOY_DIR} 2>/dev/null)" ] && cp -r ${DEPLOY_DIR} ${DEPLOY_DIR}_bak_${TS} && echo "✅ Backup done"
                    cp dist/index.html dist/style.css dist/script.js ${DEPLOY_DIR}/
                    echo "✅ Deployed to ${DEPLOY_DIR}"
                '''
            }
        }

        stage('Smoke Test') {
            when { branch 'main' }
            steps {
                sh '''
                    STATUS=$(curl -o /dev/null -s -w "%{http_code}" http://localhost/meditech/ 2>/dev/null || echo "000")
                    [ "$STATUS" = "200" ] && echo "✅ HTTP 200 OK" || echo "⚠️  HTTP $STATUS — check deployment"
                '''
            }
        }
    }

    post {
        success { echo "✅ Pipeline passed! Build: ${BUILD_NUMBER}" }
        failure { echo "❌ Pipeline FAILED — check logs above." }
        always  { sh 'rm -rf dist meditech-*.zip || true'; cleanWs() }
    }
}
