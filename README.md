# Verificar_permissoes-junto-a-pastas-
Este script é para ajudar na verificação de pastas ou sub-pastas que muitas vezes são criadas sem as permissoes necessarias impactando nos acesso  de outras aréas demandando retrabalho a equipes de TI 
#!/bin/bash

# Script para verificar permissões de pastas e arquivos e permitir alterações
# Uso: ./verificar_permissoes.sh /caminho/da/pasta

# Verifica se o usuário informou um caminho
if [ -z "$1" ]; then
    echo "Uso: $0 /caminho/da/pasta"
    exit 1
fi

PASTA="$1"

# Verifica se a pasta existe
if [ ! -d "$PASTA" ]; then
    echo "Erro: O caminho '$PASTA' não é uma pasta válida."
    exit 1
fi

echo "Listando permissões em: $PASTA"
echo "------------------------------------------------------------"

# Lista permissões de forma recursiva
find "$PASTA" -exec ls -ld {} \;

echo "------------------------------------------------------------"
read -p "Deseja alterar permissões? (s/n): " RESPOSTA

if [[ "$RESPOSTA" =~ ^[Ss]$ ]]; then
    echo "Escolha o que deseja alterar:"
    echo "Alterar permissões de TODOS os arquivos"
    echo "Alterar permissões de TODAS as pastas"
    echo "Alterar permissões de um arquivo/pasta específico"
    read -p "Opção [1/2/3]: " OPCAO

    case "$OPCAO" in
        1)
            read -p "Digite as novas permissões (ex: 644): " PERM
            find "$PASTA" -type f -exec chmod $PERM {} \;
            echo "Permissões dos arquivos alteradas para $PERM."
            ;;
        2)
            read -p "Digite as novas permissões (ex: 755): " PERM
            find "$PASTA" -type d -exec chmod $PERM {} \;
            echo "Permissões das pastas alteradas para $PERM."
            ;;
        3)
            read -p "Digite o caminho completo do arquivo/pasta: " ALVO
            if [ -e "$ALVO" ]; then
                read -p "Digite as novas permissões (ex: 755): " PERM
                chmod $PERM "$ALVO"
                echo "Permissões de '$ALVO' alteradas para $PERM."
            else
                echo "Caminho '$ALVO' não encontrado."
            fi
            ;;
        *)
            echo "Opção inválida."
            ;;
    esac
else
    echo "Nenhuma alteração feita. Apenas listagem concluída."
fi
