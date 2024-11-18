## WSL-CLI | Configuração para acessar serviços que estão fora do ambiente do Linux virtualizado

### Identificar o Endereço IP do Windows
No WSL, o hostname localhost aponta para o ambiente Linux, não para o Windows. Você precisa usar o endereço IP do Windows.

Execute este comando no WSL para obter o IP do Windows:

```bash
cat /etc/resolv.conf | grep nameserver | awk '{print $2}'
```
O comando retornará algo como 192.168.x.x. Este é o IP do Windows para o WSL.

### Configurar a String de Conexão
Exemplo de string de conexão do seu MySQL para usar este IP:

```bash
Server=192.168.x.x;Port=3306;Database=nome_do_banco;User Id=usuario;Password=senha;
```
Certifique-se de substituir 192.168.x.x pelo IP obtido no passo anterior.

### Permitir Conexões do WSL no MySQL
O MySQL, por padrão, pode estar configurado para escutar apenas no localhost do Windows. Você precisa permitir conexões de outros IPs.

1 - Editar o arquivo de configuração do MySQL (my.ini) No Windows, edite o arquivo my.ini que geralmente fica em:
```bash
C:\ProgramData\MySQL\MySQL Server X.X\my.ini
```
Procure a linha:
```bash
bind-address = 127.0.0.1
```
Altere para:
```bash
bind-address = 0.0.0.0
```
2 - Reinicie o serviço MySQL Após salvar as alterações, reinicie o serviço MySQL no Windows:
```bash
net stop mysql
net start mysql
```
3 - Garantir permissões para o usuário Certifique-se de que o usuário do MySQL pode acessar de qualquer lugar:

```bash
GRANT ALL PRIVILEGES ON *.* TO 'usuario'@'%' IDENTIFIED BY 'senha';
FLUSH PRIVILEGES;
```
4 - Testar a Conexão
No WSL, teste a conexão ao banco usando o mysql CLI ou o cliente do seu aplicativo:
```bash
mysql -h 192.168.x.x -P 3306 -u usuario -p
```
Se funcionar, sua aplicação também deve conseguir se conectar.

5. Opção Alternativa: Instalar o MySQL no WSL
Se preferir evitar essa configuração, você pode instalar o MySQL diretamente no WSL. No Ubuntu do WSL, use:
```bash
sudo apt update
sudo apt install mysql-server
sudo service mysql start
```
Isso elimina a necessidade de comunicação com o MySQL no Windows, mas pode ser uma solução mais complexa se o banco de dados precisar ser compartilhado com outros aplicativos no Windows.
