# solicitacao-de-mmh-gracilianomuniz-app
Sistema de requisição e entrega de materiais hospitalares
import { useState } from 'react';
import { Search, Plus, Minus, Check, Clipboard, Send, Clock, UserCheck } from 'lucide-react';

export default function HospitalRequisitionApp() {
  const [selectedTab, setSelectedTab] = useState('medicacao');
  const [searchTerm, setSearchTerm] = useState('');
  const [selectedItems, setSelectedItems] = useState([]);
  const [urgencyLevel, setUrgencyLevel] = useState('normal');
  const [requesterName, setRequesterName] = useState('');
  const [requestNotes, setRequestNotes] = useState('');
  const [showConfirmation, setShowConfirmation] = useState(false);
  const [activeView, setActiveView] = useState('request'); // 'request' ou 'delivery'
  const [deliveryPerson, setDeliveryPerson] = useState('');
  const [pendingRequests, setPendingRequests] = useState([
    { 
      id: 'SOL-0123', 
      requesterName: 'Dr. Silva', 
      date: '27/04/2025 09:15', 
      urgency: 'alta',
      status: 'pendente',
      items: [
        { id: 1, nome: 'Adrenalina 1mg/ml', quantidade: 2 },
        { id: 103, nome: 'Cateter intravenoso 18G', quantidade: 5 }
      ]
    },
    { 
      id: 'SOL-0122', 
      requesterName: 'Enf. Santos', 
      date: '27/04/2025 08:30', 
      urgency: 'normal',
      status: 'pendente',
      items: [
        { id: 5, nome: 'Adenosina 3mg/ml', quantidade: 1 },
        { id: 105, nome: 'Seringa 5ml', quantidade: 10 }
      ]
    }
  ]);
  
  // Lista de medicamentos da maleta de emergência
  const medicacoes = [
    { id: 1, nome: 'Adrenalina 1mg/ml', categoria: 'Emergência', estoque: 10 },
    { id: 2, nome: 'Atropina 0,5mg/ml', categoria: 'Emergência', estoque: 8 },
    { id: 3, nome: 'Amiodarona 50mg/ml', categoria: 'Emergência', estoque: 5 },
    { id: 4, nome: 'Lidocaína 2%', categoria: 'Emergência', estoque: 7 },
    { id: 5, nome: 'Adenosina 3mg/ml', categoria: 'Emergência', estoque: 4 },
    { id: 6, nome: 'Morfina 10mg/ml', categoria: 'Analgésico', estoque: 6 },
    { id: 7, nome: 'Diazepam 5mg/ml', categoria: 'Sedativo', estoque: 9 },
    { id: 8, nome: 'Furosemida 10mg/ml', categoria: 'Diurético', estoque: 12 },
    { id: 9, nome: 'Hidrocortisona 100mg', categoria: 'Anti-inflamatório', estoque: 8 },
    { id: 10, nome: 'Glicose 50%', categoria: 'Metabólico', estoque: 15 },
  ];
  
  // Lista de materiais médico-hospitalares
  const materiais = [
    { id: 101, nome: 'Cateter intravenoso 14G', categoria: 'Acesso Vascular', estoque: 20 },
    { id: 102, nome: 'Cateter intravenoso 16G', categoria: 'Acesso Vascular', estoque: 18 },
    { id: 103, nome: 'Cateter intravenoso 18G', categoria: 'Acesso Vascular', estoque: 25 },
    { id: 104, nome: 'Cateter intravenoso 20G', categoria: 'Acesso Vascular', estoque: 30 },
    { id: 105, nome: 'Seringa 5ml', categoria: 'Material de Injeção', estoque: 50 },
    { id: 106, nome: 'Seringa 10ml', categoria: 'Material de Injeção', estoque: 45 },
    { id: 107, nome: 'Luvas estéreis M (par)', categoria: 'EPI', estoque: 40 },
    { id: 108, nome: 'Máscara de oxigênio', categoria: 'Respiratório', estoque: 15 },
    { id: 109, nome: 'Tubo endotraqueal 7.5', categoria: 'Respiratório', estoque: 8 },
    { id: 110, nome: 'Ambu adulto', categoria: 'Respiratório', estoque: 5 },
  ];
  
  const listaExibida = selectedTab === 'medicacao' ? medicacoes : materiais;
  
  const itensFiltrados = listaExibida.filter(item => 
    item.nome.toLowerCase().includes(searchTerm.toLowerCase()) ||
    item.categoria.toLowerCase().includes(searchTerm.toLowerCase())
  );
  
  const adicionarItem = (item) => {
    const itemExistente = selectedItems.find(i => i.id === item.id);
    if (itemExistente) {
      setSelectedItems(selectedItems.map(i => 
        i.id === item.id ? {...i, quantidade: i.quantidade + 1} : i
      ));
    } else {
      setSelectedItems([...selectedItems, {...item, quantidade: 1}]);
    }
  };
  
  const removerItem = (item) => {
    const itemExistente = selectedItems.find(i => i.id === item.id);
    if (itemExistente.quantidade > 1) {
      setSelectedItems(selectedItems.map(i => 
        i.id === item.id ? {...i, quantidade: i.quantidade - 1} : i
      ));
    } else {
      setSelectedItems(selectedItems.filter(i => i.id !== item.id));
    }
  };
  
  const enviarSolicitacao = () => {
    // Em uma implementação real, aqui enviaríamos os dados para um backend
    const novaSolicitacao = {
      id: `SOL-${Math.floor(Math.random() * 10000).toString().padStart(4, '0')}`,
      requesterName,
      date: new Date().toLocaleString('pt-BR', {
        day: '2-digit',
        month: '2-digit',
        year: 'numeric',
        hour: '2-digit',
        minute: '2-digit'
      }),
      urgency: urgencyLevel,
      status: 'pendente',
      items: selectedItems
    };
    
    console.log("Solicitação enviada:", novaSolicitacao);
    
    // Adicionar à lista de solicitações pendentes
    setPendingRequests([novaSolicitacao, ...pendingRequests]);
    
    setShowConfirmation(true);
    
    // Resetar formulário após 3 segundos
    setTimeout(() => {
      setSelectedItems([]);
      setUrgencyLevel('normal');
      setRequestNotes('');
      setShowConfirmation(false);
    }, 3000);
  };
  
  const registrarEntrega = (requestId) => {
    if (!deliveryPerson.trim()) {
      alert("Por favor, informe o nome de quem está realizando a entrega.");
      return;
    }
    
    setPendingRequests(pendingRequests.map(req => 
      req.id === requestId ? {
        ...req, 
        status: 'entregue', 
        deliveryPerson,
        deliveryDate: new Date().toLocaleString('pt-BR', {
          day: '2-digit',
          month: '2-digit',
          year: 'numeric',
          hour: '2-digit',
          minute: '2-digit'
        })
      } : req
    ));
    
    // Limpar campo após registrar
    setDeliveryPerson('');
  };
  
  const renderRequestView = () => (
    <>
      {/* Abas de navegação */}
      <div className="flex border-b">
        <button 
          className={`flex-1 py-2 font-medium ${selectedTab === 'medicacao' ? 'text-blue-600 border-b-2 border-blue-600' : 'text-gray-500'}`}
          onClick={() => setSelectedTab('medicacao')}
        >
          Medicações
        </button>
        <button 
          className={`flex-1 py-2 font-medium ${selectedTab === 'material' ? 'text-blue-600 border-b-2 border-blue-600' : 'text-gray-500'}`}
          onClick={() => setSelectedTab('material')}
        >
          Materiais
        </button>
      </div>
      
      {/* Barra de pesquisa */}
      <div className="p-4 flex items-center bg-white border-b">
        <Search className="w-5 h-5 text-gray-400 mr-2" />
        <input
          type="text"
          placeholder={`Buscar ${selectedTab === 'medicacao' ? 'medicações' : 'materiais'}...`}
          className="w-full p-2 outline-none"
          value={searchTerm}
          onChange={(e) => setSearchTerm(e.target.value)}
        />
      </div>
      
      {/* Lista de itens */}
      <div className="flex-1 overflow-y-auto">
        {itensFiltrados.map(item => (
          <div key={item.id} className="flex items-center justify-between p-4 border-b hover:bg-gray-100">
            <div>
              <h3 className="font-medium">{item.nome}</h3>
              <p className="text-sm text-gray-500">
                Categoria: {item.categoria} | Estoque: {item.estoque}
              </p>
            </div>
            <div className="flex items-center">
              <button 
                onClick={() => adicionarItem(item)} 
                className="p-1 bg-blue-100 text-blue-600 rounded-full"
              >
                <Plus className="w-5 h-5" />
              </button>
            </div>
          </div>
        ))}
      </div>
      
      {/* Seção dos itens selecionados */}
      <div className="bg-white border-t border-gray-200">
        <div className="p-4 border-b">
          <h3 className="font-bold text-gray-700 mb-2">Itens selecionados: {selectedItems.length}</h3>
          
          {selectedItems.length > 0 ? (
            <div className="max-h-40 overflow-y-auto">
              {selectedItems.map(item => (
                <div key={item.id} className="flex items-center justify-between py-2">
                  <span className="flex-1">{item.nome}</span>
                  <div className="flex items-center">
                    <button
                      onClick={() => removerItem(item)}
                      className="p-1 text-gray-500 hover:text-red-500"
                    >
                      <Minus className="w-4 h-4" />
                    </button>
                    <span className="mx-2 font-medium">{item.quantidade}</span>
                    <button
                      onClick={() => adicionarItem(item)}
                      className="p-1 text-gray-500 hover:text-green-500"
                    >
                      <Plus className="w-4 h-4" />
                    </button>
                  </div>
                </div>
              ))}
            </div>
          ) : (
            <p className="text-gray-500 italic">Nenhum item selecionado</p>
          )}
        </div>
        
        {/* Formulário de solicitação */}
        <div className="p-4">
          <div className="mb-4">
            <label className="block text-sm font-medium text-gray-700 mb-1">
              Nome do solicitante: <span className="text-red-500">*</span>
            </label>
            <input
              type="text"
              className="w-full p-2 border rounded"
              value={requesterName}
              onChange={(e) => setRequesterName(e.target.value)}
              placeholder="Digite seu nome completo"
            />
          </div>
          
          <div className="mb-4">
            <label className="block text-sm font-medium text-gray-700 mb-1">
              Nível de Urgência:
            </label>
            <div className="flex">
              <button
                className={`flex-1 py-1 ${urgencyLevel === 'baixa' ? 'bg-green-100 text-green-800 font-medium' : 'bg-gray-100'}`}
                onClick={() => setUrgencyLevel('baixa')}
              >
                Baixa
              </button>
              <button
                className={`flex-1 py-1 ${urgencyLevel === 'normal' ? 'bg-blue-100 text-blue-800 font-medium' : 'bg-gray-100'}`}
                onClick={() => setUrgencyLevel('normal')}
              >
                Normal
              </button>
              <button
                className={`flex-1 py-1 ${urgencyLevel === 'alta' ? 'bg-red-100 text-red-800 font-medium' : 'bg-gray-100'}`}
                onClick={() => setUrgencyLevel('alta')}
              >
                Alta
              </button>
            </div>
          </div>
          
          <div className="mb-4">
            <label className="block text-sm font-medium text-gray-700 mb-1">
              Observações:
            </label>
            <textarea
              className="w-full p-2 border rounded"
              rows="2"
              value={requestNotes}
              onChange={(e) => setRequestNotes(e.target.value)}
              placeholder="Informações adicionais..."
            ></textarea>
          </div>
          
          <button
            className={`w-full py-3 font-medium text-white rounded flex items-center justify-center
              ${(selectedItems.length > 0 && requesterName) ? 'bg-blue-600 hover:bg-blue-700' : 'bg-gray-300 cursor-not-allowed'}`}
            disabled={selectedItems.length === 0 || !requesterName}
            onClick={enviarSolicitacao}
          >
            <Send className="w-5 h-5 mr-2" />
            Enviar Solicitação
          </button>
        </div>
      </div>
    </>
  );
  
  const renderDeliveryView = () => (
    <>
      <div className="p-4 bg-white border-b">
        <h3 className="font-bold text-gray-700 mb-2">Solicitações Pendentes</h3>
        
        {pendingRequests.length > 0 ? (
          <div className="max-h-full overflow-y-auto">
            {pendingRequests.map(request => (
              <div key={request.id} className={`mb-4 p-3 rounded-lg border ${
                request.status === 'entregue' ? 'bg-gray-50 border-gray-200' : 
                request.urgency === 'alta' ? 'bg-red-50 border-red-200' : 
                request.urgency === 'normal' ? 'bg-blue-50 border-blue-200' : 
                'bg-green-50 border-green-200'
              }`}>
                <div className="flex justify-between items-start mb-2">
                  <div>
                    <h4 className="font-bold">{request.id}</h4>
                    <p className="text-sm">
                      <span className="font-medium">Solicitante:</span> {request.requesterName}
                    </p>
                    <p className="text-sm">
                      <span className="font-medium">Data:</span> {request.date}
                    </p>
                    <p className="text-sm">
                      <span className="font-medium">Urgência:</span>{' '}
                      <span className={
                        request.urgency === 'alta' ? 'text-red-700' : 
                        request.urgency === 'normal' ? 'text-blue-700' : 
                        'text-green-700'
                      }>
                        {request.urgency.charAt(0).toUpperCase() + request.urgency.slice(1)}
                      </span>
                    </p>
                  </div>
                  <div className={`px-2 py-1 rounded text-xs font-medium ${
                    request.status === 'entregue' ? 'bg-green-100 text-green-800' : 
                    'bg-yellow-100 text-yellow-800'
                  }`}>
                    {request.status === 'entregue' ? 'Entregue' : 'Pendente'}
                  </div>
                </div>
                
                <div className="mb-3">
                  <h5 className="font-medium text-sm mb-1">Itens solicitados:</h5>
                  <ul className="text-sm pl-2">
                    {request.items.map(item => (
                      <li key={item.id} className="flex justify-between py-1">
                        <span>{item.nome}</span>
                        <span className="font-medium">Qtd: {item.quantidade}</span>
                      </li>
                    ))}
                  </ul>
                </div>
                
                {request.status === 'entregue' ? (
                  <div className="text-sm bg-green-50 p-2 rounded">
                    <p><span className="font-medium">Entregue por:</span> {request.deliveryPerson}</p>
                    <p><span className="font-medium">Data de entrega:</span> {request.deliveryDate}</p>
                  </div>
                ) : (
                  <div className="flex items-center mt-2">
                    <input 
                      type="text" 
                      className="flex-1 p-2 border rounded-l"
                      placeholder="Nome de quem está entregando"
                      value={deliveryPerson}
                      onChange={(e) => setDeliveryPerson(e.target.value)}
                    />
                    <button 
                      className="bg-green-600 hover:bg-green-700 text-white p-2 rounded-r"
                      onClick={() => registrarEntrega(request.id)}
                    >
                      <UserCheck className="w-5 h-5" />
                    </button>
                  </div>
                )}
              </div>
            ))}
          </div>
        ) : (
          <p className="text-gray-500 italic">Nenhuma solicitação pendente</p>
        )}
      </div>
    </>
  );
  
  return (
    <div className="flex flex-col h-screen max-w-md mx-auto bg-gray-50">
      {/* Cabeçalho */}
      <header className="bg-blue-600 text-white p-4">
        <h1 className="text-xl font-bold text-center">Posto Graciliano Muniz</h1>
        <h2 className="text-center">Sistema de Solicitação de Material</h2>
      </header>
      
      {/* Seleção de visualização */}
      <div className="flex border-b bg-gray-100">
        <button 
          className={`flex-1 py-3 font-medium flex items-center justify-center ${activeView === 'request' ? 'text-blue-600 bg-white' : 'text-gray-600'}`}
          onClick={() => setActiveView('request')}
        >
          <Send className="w-4 h-4 mr-1" />
          Solicitar
        </button>
        <button 
          className={`flex-1 py-3 font-medium flex items-center justify-center ${activeView === 'delivery' ? 'text-blue-600 bg-white' : 'text-gray-600'}`}
          onClick={() => setActiveView('delivery')}
        >
          <UserCheck className="w-4 h-4 mr-1" />
          Entregar
        </button>
      </div>
      
      {/* Renderização condicional baseada na view ativa */}
      {activeView === 'request' ? renderRequestView() : renderDeliveryView()}
      
      {/* Modal de confirmação */}
      {showConfirmation && (
        <div className="fixed inset-0 flex items-center justify-center bg-black bg-opacity-50">
          <div className="bg-white p-6 rounded-lg shadow-lg max-w-sm w-full">
            <div className="flex items-center justify-center text-green-500 mb-4">
              <div className="rounded-full bg-green-100 p-2">
                <Check className="w-8 h-8" />
              </div>
            </div>
            <h3 className="text-lg font-bold text-center mb-2">Solicitação Enviada</h3>
            <p className="text-gray-600 text-center mb-4">
              Sua solicitação foi registrada com sucesso e será processada em breve.
            </p>
            <div className="border-t pt-4">
              <p className="text-sm font-medium text-gray-700">Número da solicitação:</p>
              <div className="flex items-center justify-between mt-1 bg-gray-100 p-2 rounded">
                <code className="text-sm">#SOL-{Math.floor(Math.random() * 10000).toString().padStart(4, '0')}</code>
                <button className="text-blue-600">
                  <Clipboard className="w-4 h-4" />
                </button>
              </div>
            </div>
          </div>
        </div>
      )}
    </div>
  );
}
