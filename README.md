# ECHOESPRINTER 👻

**Nome do Projeto:** `ECHOESPRINTER`
**Descrição:** Um utilitário de demonstração de Red Team em Python, focado em Técnicas de Evasão de Defesa. O script simula o carregamento e a execução de *shellcode* diretamente na memória do processo Python, uma tática fundamental para evitar a detecção por antivírus/EDR que monitoram a execução de arquivos em disco.

**⚠️ AVISO LEGAL E ÉTICO ⚠️**
Este projeto é estritamente para **fins educacionais** e deve ser usado **apenas em ambientes controlados, como máquinas virtuais (VMs) de laboratório, CTFs ou plataformas de treinamento autorizadas (ex: TryHackMe, Hack The Box)**. O uso deste código em sistemas que você não possui ou não tem permissão explícita é ilegal e antiético.

---

## 🛠️ Tecnologias Utilizadas

* **Linguagem:** Python 3.x
* **Módulo Principal:** `ctypes` (Permite chamadas de funções da API nativa C/C++ do Windows).
* **Sistema Alvo:** Windows (O script utiliza funções da `kernel32.dll` como `VirtualAlloc` e `CreateThread`).
* **Tática ATT&CK:** [T1055 - Process Injection](https://attack.mitre.org/techniques/T1055/) (Simulando a injeção/execução de código em um processo existente).

## ✨ Como Funciona

1.  **Alocação de Memória:** O script chama a função **`VirtualAlloc`** da API do Windows para reservar um bloco de memória virtual. Crucialmente, ele define as permissões como **`PAGE_EXECUTE_READWRITE`** (`RWX`), um indicador de código malicioso, mas necessário para que o shellcode possa ser executado.
2.  **Cópia de Dados:** O *shellcode* (uma sequência de bytes) é copiado para a região de memória alocada usando **`RtlMoveMemory`**.
3.  **Execução em Thread:** Uma nova *thread* de execução é criada usando **`CreateThread`**. O ponto de partida (Entry Point) dessa nova *thread* é o endereço de memória onde o *shellcode* foi copiado. Isso executa o código diretamente na memória do processo Python, evitando a criação de arquivos executáveis no disco.

## ⚙️ Uso em Laboratório

### Pré-requisitos
* Um sistema operacional **Windows** (recomendado: VM limpa).
* Python 3.x instalado e configurado.

### Execução
1.  Salve o código acima como `echoesprinter.py`.
2.  Execute o script no Terminal do Windows (cmd/PowerShell):

    ```bash
    python echoesprinter.py
    ```

### Resultado Esperado
Ao executar o script, se estiver em um ambiente Windows, uma **caixa de mensagem** com a mensagem "Hello, Red Team" (ou equivalente, dependendo do shellcode) deve aparecer na tela, confirmando que o código arbitrário foi executado com sucesso na memória.

## 🔄 Próximos Passos (Desenvolvimento Red Team)

Para aprimorar esta técnica, o próximo foco deve ser:

1.  **Ofuscação da Memória:** Não usar `PAGE_EXECUTE_READWRITE` diretamente. Em vez disso, alocar `RW`, copiar o *shellcode*, e então usar **`VirtualProtect`** para mudar as permissões para `RX` (Read-Execute) antes da execução, tornando a técnica mais furtiva.
2.  **Criptografia:** Criptografar o `SHELLCODE_BYTES` (usando AES ou XOR simples) e descriptografá-lo *somente* após a cópia na memória, reduzindo a chance de o *payload* ser detectado em repouso no arquivo Python.
3.  **Shellcode Real:** Substituir o shellcode de Message Box por um *payload* de C2 (Command and Control) como um *Reverse Shell* (gerado via **`msfvenom`**).
