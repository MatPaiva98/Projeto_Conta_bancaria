# Projeto_Conta_bancaria
Desafio 1: Criando um Sistema Bancário com Python. (DIO)

import os
import sys

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

def validador_cpf(cpf):
    entrada_e_sequencial = cpf == cpf[0] * len(cpf)

    if entrada_e_sequencial:
        print('Você enviou dados sequenciais.')
        sys.exit() # cancela o código 

    nove_digitos = cpf[:9]
    contador_regressivo_1 = 10

    resultado_digito_1 = 0
    for digito in nove_digitos:
        resultado_digito_1 += int(digito) * contador_regressivo_1
        contador_regressivo_1 -= 1
    digito_1 = (resultado_digito_1 * 10) % 11
    digito_1 = digito_1 if digito_1 <= 9 else 0

    dez_digitos = nove_digitos + str(digito_1)
    contador_regressivo_2 = 11

    resultado_digito_2 = 0
    for digito in dez_digitos:
        resultado_digito_2 += int(digito) * contador_regressivo_2
        contador_regressivo_2 -= 1
    digito_2 = (resultado_digito_2 * 10) % 11
    digito_2 = digito_2 if digito_2 <= 9 else 0

    cpf_gerado_pelo_calculo = f'{nove_digitos}{digito_1}{digito_2}'

    if cpf == cpf_gerado_pelo_calculo:
        return True
    else:
        return None

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
    if validador_cpf(cpf) is not True:
        return ('CPF inválido!')
    usuario = filtrar_usuario(cpf, usuarios)

    if usuario:
        return ("\n@@@ Já existe usuário com esse CPF! @@@")
        
    nome = input('Informe seu nome: ')
    data_nascimento = input('Informe a data de nascimento (dd-mm-aaaa): ')
    endereco = input('Informe seu endereço ' \
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
        print("🆕 Conta criada com sucesso! 🆕")
        return{'agencia': agencia, 'numero_conta': numero_conta, "usuario": usuario}
    return 'Usuário não encontrado!'
    
def listar_contas(contas):
    for conta in contas:
        linha = (f'Agência: {conta["agencia"]}\n'
            f'C/C: {conta["numero_conta"]}\n'
            f'Titular: {conta["usuario"]["nome"]}\n'
            '#######################################\n')
        print(linha)

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
        conta = criar_conta(AGENCIA, numero_conta, usuarios)
        if conta:
                contas.append(conta)

    elif opcao == '6':
        if contas == None:
            print('Não há contas para serem listadas')
            continue
        print(listar_contas(contas))

    elif opcao == '7':
        break

    else:
        print('Você não escolheu nenhuma das opções!\n'
              'Tente novamente!')
