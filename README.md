# Laboratório: Windows Server e Windows 7 com Docker e Dockur

## Objetivo

Executar duas máquinas virtuais Windows — Windows Server 2019 e Windows 7 — usando o projeto [Dockur Windows](https://github.com/dockur/windows), em containers Docker separados, conectados à mesma rede Docker para apoiar práticas de redes e administração de sistemas.

Ao final, o estudante deverá conseguir:

- iniciar os dois ambientes;
- identificar os endereços IP dos containers;
- verificar a conectividade entre containers;
- acessar os consoles web e, se habilitado e configurado no Windows convidado, usar RDP;
- distinguir o endereço IP do container Docker do endereço IP do Windows executado dentro do QEMU.

> **Atenção:** a rede Docker `bridge` conecta os containers. Ela não garante, por si só, que os sistemas Windows convidados estejam diretamente na mesma rede IP entre si. A rede interna das VMs é gerenciada pelo Dockur/QEMU. Confirme os endereços dentro de cada Windows com `ipconfig` antes de testar serviços entre os sistemas convidados.

## 1. Pré-requisitos

- Linux com Docker Engine e Docker Compose v2 instalados.
- CPU com virtualização habilitada e suporte a KVM.
- Permissão para acessar `/dev/kvm`.
- Memória RAM suficiente para executar as duas VMs simultaneamente. O exemplo reserva 6 GB para o Server e 4 GB para o Windows 7, além dos recursos usados pelo host.
- Conexão de rede funcional no Linux.
- Uma rede Docker livre para o laboratório.

Verifique a virtualização e o KVM:

```bash
ls -l /dev/kvm
```

Se o dispositivo não existir, confira se a virtualização está habilitada no firmware/BIOS e se o módulo KVM está carregado.

## 2. Criar a rede Docker compartilhada

Crie uma rede dedicada chamada `windows_lab`, usando a sub-rede `172.30.0.0/24`:

```bash
docker network create \
  --driver bridge \
  --subnet 172.30.0.0/24 \
  --gateway 172.30.0.1 \
  windows_lab
```

Confira a configuração:

```bash
docker network inspect windows_lab
```

> Se essa sub-rede já estiver sendo usada na rede local ou em outra rede Docker, escolha outra sub-rede privada livre e ajuste os endereços nos dois arquivos Compose.

## 3. Criar o ambiente Windows Server 2019

Crie o diretório do projeto:

```bash
mkdir -p ~/dockur/windows-server
cd ~/dockur/windows-server
```

Crie o arquivo `docker-compose.yml` com o conteúdo:

```yaml
services:
  windows-server:
    image: dockurr/windows:latest
    container_name: windows-server

    environment:
      VERSION: "2019"
      RAM_SIZE: "6G"
      CPU_CORES: "2"

    devices:
      - /dev/kvm
      - /dev/net/tun

    cap_add:
      - NET_ADMIN

    networks:
      windows_lab:
        ipv4_address: 172.30.0.10

    ports:
      - "8006:8006"
      - "3390:3389/tcp"
      - "3390:3389/udp"

    volumes:
      - ./storage:/storage

    restart: unless-stopped
    stop_grace_period: 2m

networks:
  windows_lab:
    external: true
```

Inicie o container:

```bash
docker compose up -d
```

Acompanhe os logs:

```bash
docker logs -f windows-server
```

A primeira inicialização pode demorar. Aguarde o término da preparação e o início do Windows.

## 4. Criar o ambiente Windows 7 Cliente

Crie outro diretório:

```bash
mkdir -p ~/dockur/windows7
cd ~/dockur/windows7
```

Crie um segundo arquivo `docker-compose.yml`:

```yaml
services:
  windows7:
    image: dockurr/windows:latest
    container_name: windows7

    environment:
      VERSION: "7"
      RAM_SIZE: "4G"
      CPU_CORES: "2"

    devices:
      - /dev/kvm
      - /dev/net/tun

    cap_add:
      - NET_ADMIN

    networks:
      windows_lab:
        ipv4_address: 172.30.0.20

    ports:
      - "8007:8006"
      - "3391:3389/tcp"
      - "3391:3389/udp"

    volumes:
      - ./storage:/storage

    restart: unless-stopped
    stop_grace_period: 2m

networks:
  windows_lab:
    external: true
```

Inicie o container:

```bash
docker compose up -d
```

Acompanhe os logs:

```bash
docker logs -f windows7
```

## 5. Verificar os containers e os endereços

Liste os containers:

```bash
docker ps --format 'table {{.Names}}\t{{.Status}}\t{{.Networks}}'
```

Confira os IPs e as redes:

```bash
docker inspect -f \
'{{.Name}} -> {{range $n, $v := .NetworkSettings.Networks}}{{$n}}={{$v.IPAddress}} {{end}}' \
windows-server windows7
```

O resultado esperado é semelhante a:

```text
/windows-server -> windows_lab=172.30.0.10
/windows7 -> windows_lab=172.30.0.20
```

Confira a sub-rede da rede:

```bash
docker network inspect windows_lab \
  --format '{{json .IPAM.Config}}'
```

Se os dois containers aparecerem com o mesmo endereço, confira se ambos estão ligados à rede `windows_lab`, se os endereços fixos estão definidos nos respectivos Compose e se a rede foi criada com a sub-rede correta.

## 6. Acessar os consoles web do Dockur

Descubra o endereço IP do Linux host:

```bash
hostname -I
```

A partir de um navegador que alcance o host, acesse:

- Windows Server: `http://IP_DO_LINUX:8006`
- Windows 7: `http://IP_DO_LINUX:8007`

Substitua `IP_DO_LINUX` pelo endereço real do computador Linux.

## 7. Distinguir os IPs do Docker e os IPs dos Windows

Existem duas camadas de rede:

```text
Linux host
└── Docker
    ├── container windows-server (172.30.0.10)
    │   └── QEMU
    │       └── Windows Server 2019 (IP próprio da VM)
    └── container windows7 (172.30.0.20)
        └── QEMU
            └── Windows 7 (IP próprio da VM)
```

Os endereços `172.30.0.10` e `172.30.0.20` são os IPs dos **containers Docker**. Não os configure manualmente nas interfaces dos Windows convidados só porque foram definidos no Compose.

Em cada sistema Windows, abra o Prompt de Comando e execute:

```cmd
ipconfig
```

Anote o endereço IPv4, a máscara e o gateway apresentados em cada VM. Esses são os dados a usar ao testar a comunicação entre os próprios sistemas Windows.

## 8. Testar a conectividade

### 8.1 Teste entre os containers

No Linux, verifique se ambos estão na rede:

```bash
docker network inspect windows_lab
```

Para um teste ICMP entre containers, a imagem pode não incluir `ping`. Se necessário, use temporariamente uma imagem de diagnóstico conectada à rede, ou faça os testes de serviços conforme a atividade.

### 8.2 Teste entre os Windows convidados

Dentro do Windows 7:

```cmd
ping IP_DO_WINDOWS_SERVER
```

Dentro do Windows Server:

```cmd
ping IP_DO_WINDOWS_7
```

Use os IPs obtidos por `ipconfig`, não os IPs dos containers, a menos que a atividade esteja testando especificamente a rede Docker.

Se o ping falhar:

1. confirme que ambos os Windows terminaram de inicializar;
2. confira os endereços, as máscaras e os gateways com `ipconfig`;
3. verifique se os dois sistemas estão em redes IP que tenham rota entre si;
4. confira o Firewall do Windows e as regras de entrada para ICMP;
5. verifique os logs dos containers.

Não desative o firewall permanentemente. Para atividades de laboratório, prefira habilitar regras específicas e reativar qualquer proteção desativada para diagnóstico.

> Se os Windows convidados receberem endereços de redes internas diferentes ou isoladas, conectá-los à mesma rede Docker não cria automaticamente uma rede comum entre as VMs. Nesse caso, será necessário configurar a rede virtual interna do Dockur/QEMU de forma compatível com a versão e as opções utilizadas. Consulte a [documentação do Dockur Windows](https://github.com/dockur/windows).

## 9. Acesso por RDP

O Compose publica portas do host para os serviços RDP dos containers:

| Ambiente | Endereço de acesso pelo host |
|---|---|
| Windows Server | `IP_DO_LINUX:3390` |
| Windows 7 | `IP_DO_LINUX:3391` |

Para isso funcionar, o RDP precisa estar habilitado e funcionando no Windows convidado. O mapeamento de portas não garante que o serviço esteja habilitado.

De um cliente Windows, execute:

```cmd
mstsc
```

Informe o endereço do host e a porta correspondente, por exemplo `192.168.1.10:3390`.

**Importante:** esse acesso é diferente da comunicação direta entre Windows 7 e Windows Server. Para acessar um Windows a partir do outro, use o IP da VM convidada e a porta do serviço, desde que a rede virtual permita o tráfego entre elas.

## 10. Resolução de problemas

### O container para em `Initializing Network`

Consulte os logs:

```bash
docker logs --tail 100 windows-server
docker logs --tail 100 windows7
```

Verifique também:

```bash
docker inspect windows-server
docker inspect windows7
ls -l /dev/kvm /dev/net/tun
```

Não adicione `macvlan`, `ipvlan` ou variáveis de rede do Dockur sem confirmar a necessidade e a compatibilidade com a configuração atual.

### Os dois containers apresentam o mesmo IP

Confira:

```bash
docker inspect -f \
'{{.Name}} -> {{range $n, $v := .NetworkSettings.Networks}}{{$n}}={{$v.IPAddress}} {{end}}' \
windows-server windows7
```

Confira a configuração da rede:

```bash
docker network inspect windows_lab
```

O esperado é um IP de container diferente para cada serviço. Se necessário, pare os projetos, ajuste a rede externa e os IPs fixos e suba os projetos novamente. **Não apague os diretórios `storage`**, pois eles podem conter os discos das VMs.

### Um Windows não responde ao ping

O bloqueio pode ser causado pelo firewall, por endereços IP incompatíveis ou pela separação entre a rede Docker e a rede interna das VMs. Confirme primeiro os IPs usando `ipconfig` e teste um serviço específico antes de concluir que toda a conectividade está indisponível.

### O Windows não tem Internet

Confira a configuração de rede dentro do Windows convidado e os logs do Dockur. A conectividade do container Linux não garante automaticamente a conectividade da VM Windows, pois o QEMU possui sua própria camada de rede.

## 11. Encerrar o laboratório

Para parar e remover os containers sem apagar os dados persistidos nos diretórios `storage`:

```bash
cd ~/dockur/windows-server
docker compose down

cd ~/dockur/windows7
docker compose down
```

A rede compartilhada pode ser removida quando nenhum container estiver usando-a:

```bash
docker network rm windows_lab
```

Não remova os diretórios `storage` se quiser preservar os discos virtuais e as instalações.

## 12. Sugestões de atividades

1. Registrar os endereços IP, máscaras e gateways dos dois Windows.
2. Diferenciar o endereço IP do container e o endereço IP do Windows convidado.
3. Investigar o caminho de um pacote entre as duas VMs.
4. Habilitar RDP de forma controlada e documentar as regras de firewall necessárias.
5. Configurar e testar compartilhamento de arquivos, DNS ou serviços cliente-servidor depois de confirmar a conectividade entre as VMs.
6. Capturar e analisar tráfego com Wireshark ou `tshark`, respeitando as limitações da topologia de rede usada.

## Referências

- [Dockur Windows — repositório e documentação](https://github.com/dockur/windows)
- [Docker Compose — documentação oficial](https://docs.docker.com/compose/)
- [Docker bridge networks — documentação oficial](https://docs.docker.com/engine/network/drivers/bridge/)

---

**Nota para o docente:** este roteiro configura endereços fixos para os containers Docker e expõe portas para o host. A comunicação direta entre os sistemas Windows convidados deve ser validada separadamente, porque depende da configuração de rede interna do Dockur/QEMU.