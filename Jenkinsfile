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
                    # Consultar el código HTTP de la API ignorando problemas menores de certificados si los hubiera
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
                echo '🧪 Ejecutando Pruebas Funcionales contra PokeAPI...'
                sh '''
                    python3 -c "
                    import urllib.request, urllib.error, json, ssl

                    # Crear contexto SSL que no bloquee por certificados en contenedores aislados
                    ctx = ssl.create_default_context()
                    ctx.check_hostname = False
                    ctx.verify_mode = ssl.CERT_NONE

                    # --- CASO POSITIVO: Consultar Pokémon existente (Ditto) ---
                    print('--- Caso Positivo: Consultando Ditto ---')
                    url_valida = 'https://pokeapi.co/api/v2/pokemon/ditto'
                    req = urllib.request.urlopen(url_valida, context=ctx)
                    data = json.loads(req.read().decode('utf-8'))
                    
                    name = data.get('name')
                    id_poke = data.get('id')
                    
                    assert name == 'ditto', f'Error: Se esperaba ditto pero se obtuvo {name}'
                    assert id_poke == 132, f'Error: ID esperado era 132'
                    print(f'✅ Caso Positivo OK: Pokémon {name} (ID: {id_poke}) verificado.')

                    # --- CASO NEGATIVO: Consultar Pokémon que NO existe ---
                    print('\\n--- Caso Negativo: Consultando Pokémon Inexistente ---')
                    url_invalida = 'https://pokeapi.co/api/v2/pokemon/pokemon-no-existe-12345'
                    try:
                        urllib.request.urlopen(url_invalida, context=ctx)
                        print('❌ Error: La API debió responder 404 pero respondió exitosamente.')
                        exit(1)
                    except urllib.error.HTTPError as e:
                        assert e.code == 404, f'Error: Se esperaba código 404 pero se recibió {e.code}'
                        print(f'✅ Caso Negativo OK: La API respondió adecuadamente con HTTP {e.code} (Not Found).')
                    "
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