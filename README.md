# rb1155doet
Resizable Bar no 1155 - PT BR 

Olá, este repositório contem o roteiro de como fazer o Resizable Bar no 1155. Ele é referente ao video do 1155 do ET, que seguiu o tutorial do xCuri0. 
Video do 1155 do ET: 
Github xCuri0: https://github.com/xCuri0?tab=repositories
# Atenção
Este processo irá ALTERARA parametros e configurações da BIOS do seu computador. Tenha cuidado para não BRICKAR SUA BIOS! Preste muita atenção no que está fazendo, e leia este artigo e o vídeo do tutorial POR INTEIRO antes mesmo de começar a fazer. Não nos RESPONSABILIZAMOS por QUALQUER DANO CAUSADO.

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
