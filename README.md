<?php
header('Content-Type: text/plain; charset=utf-8');


class ContaBancaria 
{
    public $numeroConta;
    public $nomeTitular;
    public $cpf;
    public $agencia;
    public $tipoConta;
    private $saldo;
    private $situacao;

    public function __construct($numeroConta, $nomeTitular, $cpf, $agencia, $tipoConta) 
    {
        $this->numeroConta = $numeroConta;
        $this->nomeTitular = $nomeTitular;
        $this->cpf = $cpf;
        $this->agencia = $agencia;
        $this->tipoConta = $tipoConta;
        $this->saldo = 0.0;
        $this->situacao = "Ativa";
    }

    public function apresentarDados() 
    {
        echo "________________________________\n";
        echo "          Banco Horizonte          \n";
        echo "______________________________________\n";
        echo "Conta: " . $this->numeroConta . "\n";
        echo "Titular: " . $this->nomeTitular . "\n";
        echo "CPF: " . $this->cpf . "\n";
        echo "Agência: " . $this->agencia . "\n";
        echo "Tipo: " . $this->tipoConta . "\n";
        echo "Saldo: R$ " . number_format($this->saldo, 2, ',', '.') . "\n";
        echo "Situação: " . $this->situacao . "\n";
        echo "______________________________________\n\n";
    }

    public function depositar($valor) 
    {
        if ($this->situacao === "Bloqueada") {
            echo "[RECUSADO] Depósito na conta {$this->numeroConta}: A conta está bloqueada.\n";
            return;
        }

        if ($valor <= 0) {
            echo "[RECUSADO] Depósito na conta {$this->numeroConta}: O valor precisa ser maior que zero.\n";
            return;
        }

        $this->saldo += $valor;
        echo "[SUCESSO] Depósito de R$ " . number_format($valor, 2, ',', '.') . " realizado na conta {$this->numeroConta}. Saldo atual: R$ " . number_format($this->saldo, 2, ',', '.') . "\n";
    }

    public function sacar($valor) 
    {
        if ($this->situacao === "Bloqueada") {
            echo "[RECUSADO] Saque na conta {$this->numeroConta}: A conta está bloqueada.\n";
            return;
        }

        if ($valor <= 0) {
            echo "[RECUSADO] Saque na conta {$this->numeroConta}: O valor precisa ser maior que zero.\n";
            return;
        }

        if ($valor > $this->saldo) {
            echo "[RECUSADO] Saque de R$ " . number_format($valor, 2, ',', '.') . " na conta {$this->numeroConta}: Saldo insuficiente (Saldo atual: R$ " . number_format($this->saldo, 2, ',', '.') . ").\n";
            return;
        }

        $this->saldo -= $valor;
        echo "[SUCESSO] Saque de R$ " . number_format($valor, 2, ',', '.') . " realizado na conta {$this->numeroConta}. Saldo atual: R$ " . number_format($this->saldo, 2, ',', '.') . "\n";
    }

    public function consultarSaldo() 
    {
        echo "[CONSULTA] Conta: {$this->numeroConta} | Titular: {$this->nomeTitular} | Saldo Atual: R$ " . number_format($this->saldo, 2, ',', '.') . "\n";
    }

    public function alterarTitular($novoNome) 
    {
        if (trim($novoNome) === "") {
            echo "[ERRO] Alteração não realizada: O novo nome não pode estar vazio.\n";
            return;
        }

        $this->nomeTitular = $novoNome;
        echo "[SUCESSO] Nome do titular atualizado com sucesso para: {$this->nomeTitular}\n";
    }

    public function bloquearConta() 
    {
        $this->situacao = "Bloqueada";
        echo "[ALERTA] A conta {$this->numeroConta} foi BLOQUEADA.\n";
    }

    public function desbloquearConta() 
    {
        $this->situacao = "Ativa";
        echo "[SUCESSO] A conta {$this->numeroConta} foi DESBLOQUEADA.\n";
    }

    public function transferir($valor, $destino) 
    {
        if ($this->situacao === "Bloqueada") {
            echo "[RECUSADO] Transferência da conta {$this->numeroConta}: A conta de origem está bloqueada.\n";
            return;
        }

        if ($destino->situacao === "Bloqueada") {
            echo "[RECUSADO] Transferência para a conta {$destino->numeroConta}: A conta de destino está bloqueada.\n";
            return;
        }

        if ($valor <= 0) {
            echo "[RECUSADO] Transferência: O valor deve ser maior que zero.\n";
            return;
        }

        if ($valor > $this->saldo) {
            echo "[RECUSADO] Transferência de R$ " . number_format($valor, 2, ',', '.') . ": Saldo insuficiente na conta {$this->numeroConta}.\n";
            return;
        }

        $this->saldo -= $valor;
        $destino->saldo += $valor;
        echo "[SUCESSO] Transferência de R$ " . number_format($valor, 2, ',', '.') . " realizada da conta {$this->numeroConta} para a conta {$destino->numeroConta}.\n";
    }

    public function emitirRelatorio() 
    {
        $this->apresentarDados();
    }
}


$conta1 = new ContaBancaria("1001", "Kellem Arielle", "123.456.789-00", "Centro", "Corrente");
$conta2 = new ContaBancaria("1002", "Raphael Augusto", "987.654.321-11", "Centro", "Poupança");
$conta3 = new ContaBancaria("1003", "Mariana Lima", "456.789.123-22", "Norte", "Corrente");

$conta1->apresentarDados();

echo "CHECKLIST:\n\n";


echo "Teste 1: Verificar saldo inicial da conta 1001:\n";
$conta1->consultarSaldo();
echo "\n";


echo "Teste 2: Depositar R$ 500,00 na conta 1001:\n";
$conta1->depositar(500.00);
echo "\n";


echo "Teste 3: Sacar R$ 100,00 da mesma conta:\n";
$conta1->sacar(100.00);
echo "\n";


echo "Teste 4: Tentar depositar R$ 0,00 na conta 1001:\n";
$conta1->depositar(0.00);
echo "\n";


echo "Teste 5: Tentar sacar R$ 1000,00 da conta 1001:\n";
$conta1->sacar(1000.00);
echo "\n";

echo "Teste 6: Alterar nome da titular da conta 1001:\n";
$conta1->alterarTitular("Kellem Arielle Silva");
echo "\n";

echo "Teste 7: Bloquear conta 1001 e tentar sacar R$ 50,00:\n";
$conta1->bloquearConta();
$conta1->sacar(50.00);
echo "\n";

echo "Teste 8: Consultar saldo de conta bloqueada:\n";
$conta1->consultarSaldo();
echo "\n";

echo "Teste 9: Desbloquear conta 1001 e depositar R$ 200,00:\n";
$conta1->desbloquearConta();
$conta1->depositar(200.00);
echo "\n";


echo "Teste 10: Depositar na conta 1002 e transferir R$ 150,00 da conta 1001 para a 1002:\n";
$conta2->depositar(200.00);
$conta1->transferir(150.00, $conta2);
echo "\n";


echo "Teste 11: Tentar transferir R$ 2000,00 da conta 1001 para a 1003:\n";
$conta1->transferir(2000.00, $conta3);
echo "\n";


echo "Teste 12: Relatório final de todas as contas pós-operações:\n\n";
$conta1->emitirRelatorio();
$conta2->emitirRelatorio();
$conta3->emitirRelatorio();
