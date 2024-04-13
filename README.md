import requests
import base64
import urllib3
import time


def obter_token():
    # Desabilitar os avisos sobre solicitações não verificadas
    urllib3.disable_warnings(urllib3.exceptions.InsecureRequestWarning)

    # URL do endpoint para obtenção de token
    url = 'https://172.31.255.2/api/' # ip do seu mk-auth

    # Dados de autenticação (client_id e client_secret)
    client_id = 'Client_Id_b02b2947e2fe0234d401284cc7a7d707' # obtenha na configuraçao da api nop regenciamento de usuario
    client_secret = 'Client_Secret_f225838c2f85a29d77820a96ba39b9ba1ff47fc0' #obtenha na configuraçao da api nop regenciamento de usuario

    # Configuração do cabeçalho de autenticação básica (Basic Auth)
    headers = {
        'Authorization': 'Basic {}'.format(base64.b64encode('{}:{}'.format(client_id, client_secret).encode()).decode()),
        'Content-Type': 'application/json'
    }

    try:
        # Fazendo a solicitação GET para obter o token
        response = requests.get(url, headers=headers, verify=False)
        
        # Verifica se a solicitação foi bem-sucedida (código de status 200)
        response.raise_for_status()
        # Extraindo o token de acesso da resposta JSON
        token = response.text
        token.replace("Token de acesso JWT: ", "")
        if token:
            print('Token de acesso JWT:', token)
            return token
        else:
            print('O token de acesso não foi encontrado na resposta.')
            
    except requests.exceptions.RequestException as e:
        # Captura de qualquer erro de solicitação (por exemplo, conexão expirada, URL inválida, etc.)
        print('Ocorreu um erro durante a solicitação:', e)

    except ValueError as e:
        # Captura de erros relacionados à decodificação do JSON
        print('Ocorreu um erro ao decodificar a resposta JSON:', e)
# Token JWT
novo_token = ""
while True:    
    # Desativa a verificação do certificado SSL
    requests.packages.urllib3.disable_warnings()

    # URL da API para enviar o comando GET
    url_api = 'https://172.31.255.2/api/cliente/listagem'

    # Cabeçalho da solicitação com o token JWT para autenticação
    headers = {
        'Authorization': f'Bearer {novo_token}'
    }

    # Faça a solicitação GET para a API com os dados fornecidos e a autenticação
    response_api = requests.get(url_api, headers=headers, verify=False)
    try:
        # Verifique se a solicitação GET foi bem-sucedida (código de status 200)
        if response_api.status_code == 200:
            # Acessar o conteúdo da resposta como um dicionário JSON
            json_content = response_api.json()
            json_content_str = str(json_content)
            if "Token invalido" in json_content_str:
                novo_token = obter_token()
            print("Conteúdo da resposta (JSON):", json_content)
            
            # Agora você pode acessar os dados como um dicionário
            # Por exemplo, para acessar o primeiro cliente retornado:
            primeiro_cliente = json_content['clientes'][0]
            print("Primeiro cliente:", primeiro_cliente)
            time.sleep(5)
        else:
            print("Erro ao enviar o comando GET para a API. Código de status:", response_api.status_code)
            
    except KeyError as res:
        print("except Key erro",res)       
