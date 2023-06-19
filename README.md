# Projeto_Conta_bancaria
Desafio 1: Criando um Sistema Bancário com Python. (DIO)

import os 

menu = """
[1] Depositar
[2] Sacar
[3] Extrato
[4] SAIR
"""

saldo = 0
saques_realizados = 0
LIMITE_SAQUE = 3
extrato = []

while True:

    opcao = input(f'{menu}\n'
                  'Escolha uma das opções: ')
    print()

    if opcao == '1':
        try:
            deposito = float(input('Informe o valor a ser depositado: '))
        except ValueError:
            print('Informe um valor válido!')
            continue
        saldo += deposito
        extrato.append(f' + {deposito:.2f}')

    elif opcao == '2':
        if saques_realizados >= LIMITE_SAQUE:
            print('Você já realizou a quantidade máxima de 3 saques diários')
            continue
        try:
            saque = float(input('Informe o valor a ser sacado: '))
            if saque > saldo:
                print('Você não possui saldo suficiente\n'
                      f'Seu saldo é de R$:{saldo:.2f}')
                continue
        except ValueError:
            print('Informe um valor válido!')
            continue
        if saque > 500:
            print('Seu limite máximo por saque é de R$ 500.00')
            continue
        saldo -= saque 
        extrato.append(f' - {saque:.2f}')
        saques_realizados += 1

    elif opcao == '3':
        os.system('clear')
        if saldo != 0:
            print('Seu extrato:')
        for valor in extrato:
            print(f'{valor}')
        print(f'Seu saldo final é: R${saldo:.2f}')

    elif opcao == '4':
        break

    elif opcao is not '1' or opcao is not '2' or opcao is not '3' or opcao is not '4':
        print('Você não escolheu nenhuma das opções!\n'
              'Tente novamente!')
