# RB1155doet
Resizable Bar no 1155 - PT BR 

Olá, este repositório contem o roteiro de como fazer o Resizable Bar no 1155. Ele é referente ao video do 1155 do ET, que seguiu o tutorial do xCuri0. Em caso de duvidas, entre em nosso servidor do Discord, ou fale diretamente com o ET pelo Discord.

Discord do 1155doET: 1155doet

Servidor do Discord: https://discord.gg/8TW3fNBGH5

Video do 1155 do ET: https://www.youtube.com/watch?v=YlzRZQTnF14&t=1728s

Github xCuri0: https://github.com/xCuri0?tab=repositories

# Atenção
Este processo irá ALTERARA parametros e configurações da BIOS do seu computador. Tenha cuidado para não BRICKAR SUA BIOS! Preste muita atenção no que está fazendo, e leia este artigo e o vídeo do tutorial POR INTEIRO antes mesmo de começar a fazer. Não nos RESPONSABILIZAMOS por QUALQUER DANO CAUSADO. 

# FAQ
Se o meu computador não dá imagem e ficar apitando o que devo fazer?
R: Tire o computador da Tomada, e clique no botão power. Remova a pilha da BIOS e espere 20 minutos. Coloque novamente a pilha e coloque o computador na tomada e tente ligar.

Se o meu computador não dá imagem e não ficar apitando o que devo fazer?
R: Neste caso uma regravação de BIOS será necessaria, mas isso depende muito de cada placa mãe. No nosso caso, tivemos que colocar o chip da BIOS em uma regravadora (maquina especifica para regravação de BIOS). 

Este procedimento funciona em placa mãe que não seja 1155 ou em Ryzen,
R: Este especifico procedimento especifico só funciona em processadores e INTEL da SEGUNDA ou TERCEIRA geração e placas mães compativeis com estes . 

# Materiais necessarios
-Windows (10 recomendado).  
-Um pendrive de no minimo 8gb.
-Para o maior desempenho, após feito o procedimento, poderá usar uma distribuição Linux de sua preferencia para rodar os jogos (por enquanto, testamos o Big Linux), mas para fazer o processo o Windows é necessario.

# Passo a passo do procedimento

1 - Baixar a BIOS original da placa mãe

2 - Above 4G Decoding. https://github.com/xCuri0/ReBarUEFI/wiki/Enabling-hidden-4G-decoding

Para ativar o Above 4G Decoding, eu baixei os 3 programas indicados no repositório do xCuri0: o UEFI Tool, o IFR Extractor e o grub-mod-setup-var. 
Com os programas já extraídos, eu abri o UEFI Tool e carreguei o arquivo da BIOS da minha placa mãe nele. Depois pesquisei por "4G Decod" para encontrar a seção onde está a opção. Selecionei a seção encontrada e depois disso, eu extraí o texto da imagem PE32 como um arquivo .bin .
Depois disso, copiei esse arquivo .bin para a pasta do IFR Extractor, abri um cmd nessa pasta e rodei o IFR Extractor, especificando o nome do arquivo que tinha salvo antes. Isso fará com que o IFR Extractor extraia os dados desse arquivo bin para um txt de forma que a gente consiga ler.
Depois disso, abri esse arquivo .txt e procurei por "4G ", e encontrei a linha onde está o nome da variável e o offset. Isso vai ser o que iremos mudar lá no GRUB modificado. Aqui no meu caso o var offset foi 0x1. 
Sendo assim, agora vou copiar os arquivos necessários para carregar o GRUB modificado lá na inicialização da BIOS, pra funcionar como se estivesse bootando um instalador do Windows ou Linux. Para isso, eu copiei o arquivo modGRUBShell.efi que baixei mais cedo para um pendrive bootavel do Windows, poderia ser Linux também. Copiei o arquivo para a pasta EFI/Boot/bootx64.efi do pendrive. Fazendo isso, quando a BIOS for ler esse pendrive, vai procurar por esse arquivo EFI, e carregá-lo normalmente só que ao invés do instalador do Windows, vai carregar nosso GRUB modificado.

Na BIOS, eu me certifiquei de que o Secure Boot tava desligado e o CSM também pra gente não ter problemas. E depois entrei no menu de boot e escolhi o pendrive. Aqui no grub, eu digitei setup_var e o offset que tinha conseguido lá no passo anterior, que no meu caso era 0x1.
Sendo assim, digitei setup_var 0x1. Com isso vamos obter o estado em que essa opção está lá na BIOS. se estiver 0x0, está desativada e se estiver 0x1, está ativada. No meu caso estava 0x0, desativada mesmo e pra ativar eu digitei setup_var 0x1 0x1, onde o primeiro 0x1 é a variável que a gente ta mexendo e o 0x1 no final é pra definir como ativado. É como se eu fosse lá na BIOS e alterasse a opção Above 4G Decoding pra ativado, mas como não tem essa opção lá, fiz por linha de comando. Aí eu reiniciei pra ver se aplicou, e pra minha surpresa, mudou de 0x0 para 0x1. Entrei lá no sistema pra ver como estava e pra minha surpresa tinha funcionado. Depois disso, só me deu mais fôlego pra continuar. 

3 - Adicionar o Módulo FFS no volume DXE da BIOS. https://github.com/xCuri0/ReBarUEFI/wiki/Adding-FFS-module

Começando, eu abri o UEFI Tool, e carreguei novamente a BIOS original da placa mãe, dessa vez a pesquisa foi por um GUID, 3C1DE39F-D207-408A-AACC-731CFB7F1DD7, que é basicamente um identificador universal, ou seja, é o mesmo pra maioria das BIOS. Esse identificador acha o módulo PciBus, que é responsável por gerenciar a PCI e PCI Express.
Encontrado o módulo PciBus, nós teremos que inserir o ReBarDxe no final do volume onde está o modulo PciBus, como se estivéssemos colocando um arquivo na mesma pasta, só que na ordem certa. Para isso, eu desci até o final desse volume, cliquei com o botão direito no último módulo do volume e cliquei em insert after, que significa que vamos inserir o módulo do Resizeable Bar depois do último módulo do volume, ou seja, vamos colocar o novo módulo no final da “pasta”. Feito isso, eu conferi se o módulo estava lá mesmo, e tudo certo. Depois fui em File e Save image file, para salvar a nova BIOS modificada.

4 - Fazer os patches necessários com o UEFI Patch. https://github.com/xCuri0/ReBarUEFI/wiki/Using-UEFIPatch

Para isso eu baixei o UEFI Patch, e o arquivo patches.txt, e coloquei os arquivos da BIOS modificada, o patches.txt e o executável do UEFI Patch todos na mesma pasta. Depois disso, abri um CMD nessa pasta e executei o UEFI Patch com o arquivo da BIOS especificado depois. Isso gerou um arquivo de BIOS com a extensão .patched no final.

5 - Conferindo se a BIOS está com a ordem dos arquivos certa. https://github.com/xCuri0/ReBarUEFI/wiki/Using-UEFIPatch#checking-for-pad-file-issue

Depois de modificarmos a BIOS várias vezes com o UEFI Tool, pode ser que aconteça um bug onde os arquivos do volume que estávamos mexendo saiam da ordem. Isso acontece principalmente em BIOS da ASUS, que é o meu caso.
Para ver se isso aconteceu, eu abri a BIOS original em um UEFI Tool e a BIOS modificada em outro UEFI Tool, procurei por aquele GUID em que nós fizemos a modificação e chequei se todos os ‘’arquivos’’ estavam na mesma ordem. Os mais importantes são os PAD Files, esses têm que estar exatamente na mesma ordem, como estavam aí no meu caso. Caso tivesse dado errado, lá no repositório do xCuri0 ele descreveu maneiras de corrigir isso. Como não é o meu caso, vamos para o próximo passo.

6 - DSDT Patches. https://github.com/xCuri0/ReBarUEFI/wiki/DSDT-Patching#sandyivy-bridge-dsdt-patch

Para saber se vamos precisar aplicar os DSDT Patches, vamos ir no Gerenciador de Dispositivos, selecionar Exibir e depois Recursos por Conexão. Depois disso vamos expandir a seção Memória Grande e procurar o Pci Bus. Caso o Pci Bus terminar com uma sequência de 9 F ou mais, entao você não precisara fazer essa parte, mas caso a sequência for menos de 9 F como no meu caso, é melhor fazer essa parte. No meu caso está com uma sequência de 6 F, então seria bom eu fazer esse patch também.
Pra fazer isso, abri o UEFI Tool e importei a última BIOS modificada que fizemos. Depois disso, pesquisei novamente por um GUID, nesse caso foi o 9F3A0016-AE55-4288-829D-D22FD344C34, que nesse caso, me levou ao módulo AmiBoardInfo. Nesse modulo, eu cliquei com o botão direito na imagem PE32 e fui em Extract Body, para extrair o conteúdo do modulo. Depois disso, salvei o conteúdo como AmiBoardInfo.efi. Depois disso, eu baixei o AmiBoardInfoTool, especificado no repositório do xCuri0, e extrai ele normalmente. Coloquei esse arquivo extraído junto ao arquivo EFI que tínhamos salvo no UEFI Tool, abri um cmd usando o menu do botão direito do Windows e rodei o comando “AmiBoardInfoTool -a AmiBoardInfo.efi -d DSDT.aml” para extrair o DSDT original da minha BIOS para o arquivo DSDT.aml. Após isso, fiz o download do programa iasl.exe do site da Intel, que também estava especificado no repositório, e rodei esse programa especificando o arquivo DSDT.aml. Fazendo isso, o programa decompilou o DSDT original da minha placa mãe para o arquivo DSDT.dsl, que conseguimos editar no notepad. Sendo assim, eu renomeei o arquivo DSDT.dsl para DSDTMod.dsl para não confundirmos o original com o modificado, e abri esse arquivo renomeado no Notepad.
Agora que vem a parte mais complicada do procedimento inteiro, confesso que eu não entendi muito bem o que tava fazendo nessa parte, mas mesmo assim, segui à risca o tutorial do xCuri0. Procurei no notepad pelo texto “CreateQWordField” e encontrei uma seção semelhante a que estava lá no tutorial. Sendo assim, fiz a modificação da linha que estava M2LN = 0x0000000400000000 para M2MX = 0xFFFFFFFFF. E salvei o arquivo. Depois disso, recompilei o DSDT usando o comando iasl DSDTMod.dsl, e obtive um erro de compilação. Lá no tutorial do xCuri0, ele disse que caso tivesse algum erro, eu tinha que consertar ele manualmente. Mas é aí que tá o problema, eu já tava todo perdido e ainda aparece esse erro.
Entao, fui bem custoso e procurei uma forma de ignorar o erro usando a ajuda do programa iasl.exe, encontrei uma forma de ignorar e fiz assim. Depois disso, recriei o arquivo AmiBoardInfo, usando o comando “AmiBoardInfoTool -a AmiBoardInfo.efi -d DSDTMod.aml -o AmiBoardInfoMod.efi”. Onde foram especificados, o arquivo efi original, o DSDT que modificamos, e o arquivo de saída. E depois inseri esse arquivo de saída de volta na nossa BIOS usando o UEFI Tool, de forma semelhante que fiz pra inserir o modulo do ResizeableBAR. 

7 - Gravar a BIOS na placa. https://github.com/xCuri0/ReBarUEFI/wiki/Flashing-modified-UEFI

Bom aqui vai depender 100% da fabricante e modelo da placa mãe, tem diversas formas de gravar uma BIOS, dentre elas, AFUDOS, AFUWIN, FPT, e gravadoras externas. Além dessas tradicionais, temos a própria seção de gravação de BIOS da placa mãe, e em alguns modelos, temos o programa da placa mãe no Windows que faz o update da BIOS.
No meu caso, estou usando uma ASUS P8H61-M LX3 R2.0, e não consegui gravar a BIOS usando a seção de update da própria BIOS. Mas a maioria das placas ASUS tem o software Ai Suite, e lá tem um programa que faz o Update da BIOS. E é aí que entra a técnica milenar encontrada também no repositório do github do xCuri0.
Eu instalei o Ai Suite, e lá fiz o "update" da BIOS, só que ao invés de seguir normalmente, eu fiz o seguinte: deixei o arquivo da BIOS original e o da BIOS modificada um do lado do outro no desktop, e na hora de selecionar o arquivo no EZ Update, eu escolhi o arquivo original, daí ele fez toda aquela checagem no arquivo e logo antes de apertar o botão Flash, eu apaguei o arquivo original e renomeei o modificado para o mesmo nome do original, sendo assim a BIOS gravou perfeitamente.
Depois de flashear a BIOS, todos os parâmetros foram limpos, sendo assim tive que reconfigurar tudo, e repetir o passo 2 que era ativar o Above 4G Decoding, mas como agora eu já tinha a variável, era só digitar lá no grub modificado e já era. 

8 – Modificar drivers AMD pelo Regedit. https://github.com/xCuri0/ReBarUEFI/wiki/Common-issues-(and-fixes)#how-do-i-enable-resizable-bar-on-unsupported-amd-gpus-

Depois disso tudo, tive que fazer mais uma modificação, mas agora foi nos drivers da AMD. Como eu tenho uma RX580 2048SP que não tem o Resizeable BAR nativamente, precisei alterar algumas chaves no registro do Windows. Pra isso, usei o arquivo .reg disponibilizado pelo xCuri0 no repositório do github. Link vai estar na descrição. Apliquei o .reg e reiniciei o PC.
