<!DOCTYPE html>
<html lang="pt-BR">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0" />
  <title>Mapeamento de Cargos & Salários 2025/2026</title>
  <script src="https://unpkg.com/react@18/umd/react.production.min.js" crossorigin></script>
  <script src="https://unpkg.com/react-dom@18/umd/react-dom.production.min.js" crossorigin></script>
  <script src="https://unpkg.com/@babel/standalone/babel.min.js"></script>
  <style>
    *, *::before, *::after { box-sizing: border-box; margin: 0; padding: 0; }
    body { font-family: system-ui, -apple-system, sans-serif; background: #f8fafc; color: #1e293b; line-height: 1.5; }
    .container { max-width: 1000px; margin: 0 auto; padding: 20px; }
    .card { background: white; border-radius: 12px; padding: 24px; box-shadow: 0 4px 6px -1px rgb(0 0 0 / 0.1); margin-bottom: 20px; }
    .btn { padding: 12px 24px; border-radius: 8px; border: none; cursor: pointer; font-weight: 600; transition: all 0.2s; }
    .btn-primary { background: #2563eb; color: white; }
    .btn-primary:hover { background: #1d4ed8; }
    .input { width: 100%; padding: 12px; border: 1px solid #e2e8f0; border-radius: 8px; margin-top: 6px; margin-bottom: 16px; font-size: 16px; }
    label { font-weight: 600; font-size: 14px; color: #475569; }
    table { width: 100%; border-collapse: collapse; margin-top: 20px; }
    th { background: #f1f5f9; padding: 12px; text-align: left; border-bottom: 2px solid #e2e8f0; }
    td { padding: 12px; border-bottom: 1px solid #e2e8f0; }
  </style>
</head>
<body>
  <div id="root"></div>

  <script type="text/babel">
    const { useState, useEffect } = React;

    // --- COMPONENTES DE INTERFACE (DEFINIDOS FORA PARA NÃO PERDER FOCO) ---
    const Field = ({ label, children }) => (
      <div style={{ marginBottom: '16px' }}>
        <label>{label}</label>
        {children}
      </div>
    );

    const TextInput = ({ value, onChange, placeholder }) => (
      <input 
        className="input" 
        type="text" 
        value={value} 
        onChange={(e) => onChange(e.target.value)} 
        placeholder={placeholder}
      />
    );

    // --- APP PRINCIPAL ---
    function App() {
      const [screen, setScreen] = useState('home');
      const [submissions, setSubmissions] = useState([]);
      const [formData, setFormData] = useState({ nome: '', cargo: '', depto: '', atividades: '' });
      const [pass, setPass] = useState('');

      useEffect(() => {
        const saved = localStorage.getItem('mapeamento_dados');
        if (saved) setSubmissions(JSON.parse(saved));
      }, []);

      const saveSubmission = () => {
        if (!formData.nome || !formData.cargo) return alert("Preencha o nome e o cargo!");
        const novoDado = { ...formData, id: Date.now(), data: new Date().toLocaleDateString() };
        const listaAtualizada = [...submissions, novoDado];
        setSubmissions(listaAtualizada);
        localStorage.setItem('mapeamento_dados', JSON.stringify(listaAtualizada));
        alert("Enviado com sucesso!");
        setFormData({ nome: '', cargo: '', depto: '', atividades: '' });
        setScreen('home');
      };

      // TELA INICIAL
      if (screen === 'home') return (
        <div className="container" style={{ textAlign: 'center', marginTop: '100px' }}>
          <h1 style={{ fontSize: '32px', marginBottom: '10px' }}>Mapeamento de Cargos & Salários</h1>
          <p style={{ color: '#64748b', marginBottom: '40px' }}>Selecione o seu perfil de acesso</p>
          <div style={{ display: 'flex', gap: '20px', justifyContent: 'center' }}>
            <button className="btn btn-primary" onClick={() => setScreen('form')}>Sou Colaborador</button>
            <button className="btn" style={{ background: '#64748b', color: 'white' }} onClick={() => setScreen('login')}>Acesso RH</button>
          </div>
        </div>
      );

      // FORMULÁRIO DO COLABORADOR
      if (screen === 'form') return (
        <div className="container">
          <div className="card">
            <button onClick={() => setScreen('home')} style={{ background: 'none', border: 'none', color: '#2563eb', cursor: 'pointer', marginBottom: '20px' }}>← Voltar</button>
            <h2>Ficha de Mapeamento</h2>
            <Field label="Nome Completo">
              <TextInput value={formData.nome} onChange={(v) => setFormData({...formData, nome: v})} placeholder="Seu nome..." />
            </Field>
            <Field label="Cargo Atual">
              <TextInput value={formData.cargo} onChange={(v) => setFormData({...formData, cargo: v})} placeholder="Ex: Analista Financeiro" />
            </Field>
            <Field label="Departamento">
              <select className="input" value={formData.depto} onChange={(e) => setFormData({...formData, depto: e.target.value})}>
                <option value="">Selecione...</option>
                <option value="Financeiro">Financeiro</option>
                <option value="RH">Recursos Humanos</option>
                <option value="Operacional">Operacional</option>
                <option value="Vendas">Vendas</option>
              </select>
            </Field>
            <Field label="Descrição das Atividades">
              <textarea className="input" style={{ minHeight: '120px' }} value={formData.atividades} onChange={(e) => setFormData({...formData, atividades: e.target.value})} placeholder="O que você faz no dia a dia?" />
            </Field>
            <button className="btn btn-primary" style={{ width: '100%' }} onClick={saveSubmission}>Enviar Ficha</button>
          </div>
        </div>
      );

      // LOGIN RH
      if (screen === 'login') return (
        <div className="container" style={{ maxWidth: '400px', marginTop: '100px' }}>
          <div className="card">
            <h2>Login RH</h2>
            <input className="input" type="password" placeholder="Senha de acesso" value={pass} onChange={(e) => setPass(e.target.value)} />
            <button className="btn btn-primary" style={{ width: '100%' }} onClick={() => pass === 'RH2026' ? setScreen('dash') : alert("Senha incorreta")}>Entrar</button>
            <button className="btn" style={{ width: '100%', marginTop: '10px', background: 'none' }} onClick={() => setScreen('home')}>Voltar</button>
          </div>
        </div>
      );

      // PAINEL RH
      if (screen === 'dash') return (
        <div className="container">
          <div style={{ display: 'flex', justifyContent: 'space-between', alignItems: 'center' }}>
            <h1>Painel de Controle RH</h1>
            <button className="btn" style={{ background: '#ef4444', color: 'white' }} onClick={() => setScreen('home')}>Sair</button>
          </div>
          <div className="card" style={{ marginTop: '20px' }}>
            <h3>Fichas Recebidas ({submissions.length})</h3>
            <table>
              <thead>
                <tr>
                  <th>Data</th>
                  <th>Nome</th>
                  <th>Cargo</th>
                  <th>Depto</th>
                </tr>
              </thead>
              <tbody>
                {submissions.map(s => (
                  <tr key={s.id}>
                    <td>{s.data}</td>
                    <td>{s.nome}</td>
                    <td>{s.cargo}</td>
                    <td>{s.depto}</td>
                  </tr>
                ))}
                {submissions.length === 0 && <tr><td colSpan="4" style={{ textAlign: 'center', color: '#94a3b8' }}>Nenhuma ficha enviada ainda.</td></tr>}
              </tbody>
            </table>
          </div>
        </div>
      );
    }

    const root = ReactDOM.createRoot(document.getElementById('root'));
    root.render(<App />);
  </script>
</body>
</html>
