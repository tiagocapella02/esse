import React, { useState } from "react";
import { Card, CardContent } from "@/components/ui/card";
import { Button } from "@/components/ui/button";
import { Input } from "@/components/ui/input";
import { PlusCircle, Trash2 } from "lucide-react";
import { FaYenSign, FaDollarSign, FaMoneyBillWave } from "react-icons/fa";

export default function CalculadoraProdutos() {
  const [produtos, setProdutos] = useState([{ valor: "", peso: "" }]);
  const [totalUSD, setTotalUSD] = useState(0);
  const [totalBRL, setTotalBRL] = useState(0);
  const [custoPorProduto, setCustoPorProduto] = useState([]);

  const cotacoes = {
    CNY: 1,
    USD: 0.14,
    BRL: 4.90,
  };

  const handleChange = (index, field, value) => {
    const novosProdutos = [...produtos];
    novosProdutos[index][field] = value;
    setProdutos(novosProdutos);
  };

  const adicionarProduto = () => {
    setProdutos([...produtos, { valor: "", peso: "" }]);
  };

  const removerProduto = (index) => {
    const novosProdutos = produtos.filter((_, i) => i !== index);
    setProdutos(novosProdutos);
  };

  const calcularTotais = () => {
    let totalUSD = 0;
    const custosIndividuais = [];

    produtos.forEach(({ valor, peso }) => {
      const valorNum = parseFloat(valor);
      const pesoNum = parseFloat(peso);

      if (!isNaN(valorNum) && !isNaN(pesoNum)) {
        const valorDolar = valorNum * cotacoes.USD;
        const taxa17 = valorDolar * 0.17;
        const taxaFixa = 7.0;
        const taxaPeso = (pesoNum / 100.0) * 1.5;
        const outrosCustos = 0.8 + 9.0;
        const custoTotalProduto = valorDolar + taxa17 + taxaFixa + taxaPeso + outrosCustos;
        totalUSD += custoTotalProduto;
        custosIndividuais.push(custoTotalProduto);
      } else {
        custosIndividuais.push(0);
      }
    });

    const totalBRLConvertido = totalUSD * cotacoes.BRL;
    setTotalUSD(Number.isFinite(totalUSD) ? totalUSD : 0);
    setTotalBRL(Number.isFinite(totalBRLConvertido) ? totalBRLConvertido : 0);
    setCustoPorProduto(custosIndividuais);
  };

  return (
    <div className="p-6 max-w-4xl mx-auto bg-gray-900 text-white min-h-screen font-sans">
      <h1 className="text-3xl font-bold mb-6 text-center text-white">Calculadora de Produtos Importados</h1>

      {produtos.map((produto, index) => (
        <Card key={index} className="mb-4 bg-gray-800 border border-gray-700 rounded-2xl shadow-md text-white">
          <CardContent className="p-4 grid grid-cols-3 gap-4 items-end">
            <div>
              <label className="text-sm font-medium text-white flex items-center gap-1"><FaYenSign /> Valor (YUAN)</label>
              <Input
                type="number"
                value={produto.valor}
                onChange={(e) => handleChange(index, "valor", e.target.value)}
                className="bg-gray-700 text-white border border-gray-600 rounded-xl"
              />
            </div>
            <div>
              <label className="text-sm font-medium text-white">Peso (g)</label>
              <Input
                type="number"
                value={produto.peso}
                onChange={(e) => handleChange(index, "peso", e.target.value)}
                className="bg-gray-700 text-white border border-gray-600 rounded-xl"
              />
            </div>
            <div className="flex gap-2">
              <Button variant="outline" onClick={() => removerProduto(index)} className="border-red-500 text-red-400 hover:bg-red-900 rounded-xl">
                <Trash2 className="w-4 h-4 mr-2" /> Remover
              </Button>
            </div>
            {custoPorProduto.length > 0 && custoPorProduto[index] !== undefined && (
              <div className="col-span-3 text-sm text-green-400 mt-2">
                Custo total: $ {custoPorProduto[index].toFixed(2)} USD (R$ {(custoPorProduto[index] * cotacoes.BRL).toFixed(2)})
              </div>
            )}
          </CardContent>
        </Card>
      ))}

      <Button className="mb-4 bg-blue-700 hover:bg-blue-800 rounded-xl px-4 py-2 text-white" onClick={adicionarProduto}>
        <PlusCircle className="w-4 h-4 mr-2" /> Adicionar Produto
      </Button>

      <div className="mb-6">
        <Button onClick={calcularTotais} className="bg-green-700 hover:bg-green-800 rounded-xl px-4 py-2 text-white">Calcular Totais</Button>
      </div>

      <Card className="bg-gray-800 border border-gray-700 rounded-2xl shadow-md text-white">
        <CardContent className="p-4">
          <h2 className="text-xl font-semibold mb-3 text-white">Resultado</h2>
          <p className="text-white flex items-center gap-2">
            <FaDollarSign /> <strong>USD</strong> -&gt; <strong>$ {totalUSD.toFixed(2)}</strong>
          </p>
          <p className="text-white flex items-center gap-2">
            <FaMoneyBillWave /> <strong>BRL</strong> -&gt; <strong>R$ {totalBRL.toFixed(2)}</strong>
          </p>
        </CardContent>
      </Card>
    </div>
  );
}
# esse
