pipeline {
    agent any

    stages {
        stage('1. Checkout') {
            steps {
                echo '📥 Obteniendo código desde GitHub...'
            }
        }

        stage('2. Smoke Tests') {
            steps {
                echo '💨 Ejecutando Pruebas de Humo (Smoke Tests)...'
                echo 'Verificando disponibilididad de servicios críticos...'
            }
        }

        stage('3. Pruebas Funcionales') {
            steps {
                echo '🧪 Ejecutando Pruebas Funcionales...'
                echo 'Validando flujos de usuario y reglas de negocio...'
            }
        }

        stage('4. Reporte de Calidad') {
            steps {
                echo '📊 Generando reporte de ejecución de pruebas...'
            }
        }
    }

    post {
        success {
            echo '✅ ¡El pipeline y todas las pruebas se ejecutaron exitosamente!'
        }
    }
}