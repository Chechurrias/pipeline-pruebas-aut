pipeline {
    agent any

    stages {
        stage('1. Checkout') {
            steps {
                echo '📥 Obteniendo código desde GitHub...'
            }
        }

        stage('2. Smoke Test (PokeAPI Status)') {
            steps {
                echo '💨 Ejecutando Smoke Test: Verificando disponibilidad con curl...'
                sh '''
                    HTTP_CODE=$(curl -s -o /dev/null -w "%{http_code}" https://pokeapi.co/api/v2/)
                    echo "Código HTTP recibido: $HTTP_CODE"
                    
                    if [ "$HTTP_CODE" -eq 200 ]; then
                        echo "✅ Smoke Test OK - La PokeAPI está en línea y responde correctamente."
                    else
                        echo "❌ Smoke Test Falló - La API respondió con código: $HTTP_CODE"
                        exit 1
                    fi
                '''
            }
        }

        stage('3. Pruebas Funcionales (Positivas y Negativas)') {
            steps {
                echo '🧪 Ejecutando Pruebas Funcionales contra PokeAPI usando curl...'
                sh '''
                    # --- CASO POSITIVO: Consultar Pokémon existente (Ditto) ---
                    echo "--- Caso Positivo: Consultando Ditto ---"
                    RESPONSE=$(curl -s https://pokeapi.co/api/v2/pokemon/ditto)
                    
                    # Validar datos clave con grep de forma sencilla
                    if echo "$RESPONSE" | grep -q '"name":"ditto"' && echo "$RESPONSE" | grep -q '"id":132'; then
                        echo "✅ Caso Positivo OK: Pokémon ditto (ID: 132) verificado correctamente."
                    else
                        echo "❌ Error en caso positivo: No se encontraron los datos esperados de Ditto."
                        exit 1
                    fi

                    # --- CASO NEGATIVO: Consultar Pokémon que NO existe ---
                    echo ""
                    echo "--- Caso Negativo: Consultando Pokémon Inexistente ---"
                    HTTP_CODE_NEG=$(curl -s -o /dev/null -w "%{http_code}" https://pokeapi.co/api/v2/pokemon/pokemon-no-existe-12345)
                    echo "Código HTTP recibido para recurso inválido: $HTTP_CODE_NEG"

                    if [ "$HTTP_CODE_NEG" -eq 404 ]; then
                        echo "✅ Caso Negativo OK: La API respondió adecuadamente con HTTP 404 (Not Found)."
                    else
                        echo "❌ Error: Se esperaba código 404 pero se recibió $HTTP_CODE_NEG"
                        exit 1
                    fi
                '''
            }
        }

        stage('4. Reporte de Calidad') {
            steps {
                echo '📊 Consolidando métricas y resultados de las pruebas...'
            }
        }
    }

    post {
        success {
            echo '🎉 ¡Pipeline completado! Casos positivos y negativos validados correctamente.'
        }
        failure {
            echo '❌ El pipeline falló durante las validaciones de las pruebas.'
        }
    }
}