# Projeto_Conta_bancaria
Desafio 1: Criando um Sistema Bancário com Python. (DIO)

import os 

menu = """
[1] Depositar
[2] Sacar
[3] Extrato
[4] Novo Usuário
[5] Nova Conta
[6] Listar contas
[7] SAIR

"""

saldo_total = 0
saques_realizados = 0
LIMITE_SAQUE = 500.00
valor_final = ''
usuarios = []
AGENCIA = '0001'
numero_conta = 0
contas = []

def sacar(*, saldo, valor, extrato, limite, numero_saques):
    global saldo_total
    global valor_final
    try:
        valor_float = float(valor)
    except ValueError:
        return 'Informe um valor válido!'
    if valor_float > saldo:
        return ('Saldo insuficiente!\n'
                f'Seu saldo é de R$ {saldo:.2f}')
    elif valor_float > limite:
        return f'Seu limite máximo por saque é de R$ {limite:.2f}'
    elif numero_saques > 3:
        return (f'Esse é seu {numero_saques} saque.\n'
                'Você só pode realizar 3 saques por dia!')
    saldo_total = saldo - valor_float
    extrato += f'Saque (-): R$ {valor_float:.2f}\n'
    valor_final = extrato
    return (f'Você sacou R$ {valor_float:.2f}\n')

def depositar(saldo, valor, extrato):
    global saldo_total
    global valor_final
    try:
        valor_depositado = float(valor)
    except ValueError:
        return 'Informe um valor válido!'
    saldo_total = saldo + valor_depositado
    extrato += f'Deposito (+): R$ {valor_depositado:.2f}\n'
    valor_final = extrato
    return (f'Você depositou R$ {valor_depositado:.2f}\n')

def final(saldo, /, *, extrato):
    global valor_final
    global saldo_total
    saldo_total = saldo
    valor_final = extrato
    os.system('clear')
    return (f'####### Seu extrato: #######\n'
                     f'{extrato}\n'
          f'Seu saldo final é: R${saldo:.2f}\n'
           '############################')
    
def criar_usuario(usuario):
    cpf = input('Informe seu CPF: ').replace('.', '').replace('-', '')
    usuario = filtrar_usuario(cpf, usuarios)

    if usuario:
        return ("\n@@@ Já existe usuário com esse CPF! @@@")
        
    nome = input('Informe seu nome: ')
    data_nascimento = input('Informe a data de nascimento (dd-mm-aaaa): ')
    endereco = input('Informe seu endereço' \
                     '(logradouro, nro - bairro - cidade/sigla estado): ')
    
    usuarios.append({"nome": nome, "data_nascimento": data_nascimento, \
                    "cpf": cpf, "endereco": endereco})
    return ('✅ Usuário criado com sucesso! ✅')

def filtrar_usuario(cpf, usuarios):
    usuarios_filtrados = [usuario for usuario in usuarios if usuario["cpf"] == cpf]
    return usuarios_filtrados[0] if usuarios_filtrados else None

def criar_conta(agencia, numero_conta, usuario):
    cpf = input('Informe seu CPF: ').replace('.', '').replace('-', '')
    usuario = filtrar_usuario(cpf, usuarios)

    if usuario:
        contas.append({'agencia': agencia, 'numero_conta': numero_conta, "usuario": usuario['nome']})
        return ("@@@ Conta criada com sucesso! @@@\n")
    else:
        return 'Usuário não encontrado!'
    
def listar_contas(contas):
    for conta in contas:
        
        linha = (f'Agência: {conta["agencia"]}\n'
            f'C/C: {conta["numero_conta"]}\n'
            f'Titular: {conta["usuario"]}\n'
            '#######################################')
        return linha

while True:

    opcao = input(f'{menu}\n'
                  'Escolha uma das opções: ')
    print()

    if opcao == '1':
        valor_depositado = input('Informe o valor que deseja depositar: ')
        print(depositar(saldo_total, valor_depositado, valor_final))

    elif opcao == '2':
        saques_realizados += 1
        valor_sacado = input('Informe o valor a ser sacado: ')
        print(sacar(saldo=saldo_total, valor=valor_sacado, extrato=valor_final,
                    limite=LIMITE_SAQUE, numero_saques=saques_realizados))

    elif opcao == '3':
        os.system('clear')
        print(final(saldo_total, extrato=valor_final))

    elif opcao == '4':
        print(criar_usuario(usuarios))

    elif opcao == '5':
        numero_conta += 1
        print(criar_conta(AGENCIA, numero_conta, usuarios))

    elif opcao == '6':
        if contas == None:
            print('Não há contas para serem listadas')
            continue
        print(listar_contas(contas))

    elif opcao == '7':
        break

    elif opcao is not '1' or opcao is not '2' or opcao is not '3' or opcao is not '4'\
        or opcao is not '5' or opcao is not '6' or opcao is not '7':
        print('Você não escolheu nenhuma das opções!\n'
              'Tente novamente!')
