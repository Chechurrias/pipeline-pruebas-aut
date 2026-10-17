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
                echo '💨 Ejecutando Smoke Test: Verificando disponibilidad de PokeAPI...'
                sh '''
                    python3 -c "
                    import urllib.request
                    try:
                        response = urllib.request.urlopen('https://pokeapi.co/api/v2/')
                        print(f'✅ Smoke Test OK - Status Code: {response.getcode()}')
                    except Exception as e:
                        print(f'❌ Smoke Test Falló: {e}')
                        exit(1)
                    "
                '''
            }
        }

        stage('3. Pruebas Funcionales (Positivas y Negativas)') {
            steps {
                echo '🧪 Ejecutando Pruebas Funcionales contra PokeAPI...'
                sh '''
                    python3 -c "
                    import urllib.request, urllib.error, json

                    # --- CASO POSITIVO: Consultar Pokémon existente (Ditto) ---
                    print('--- Caso Positivo: Consultando Ditto ---')
                    url_valida = 'https://pokeapi.co/api/v2/pokemon/ditto'
                    req = urllib.request.urlopen(url_valida)
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
                        urllib.request.urlopen(url_invalida)
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