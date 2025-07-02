# Estoque
# FUNÇÕES DO SISTEMA ESTOQUE120394TESTE

## Documentação das Funcionalidades com Códigos

---

## 1. AUTENTICAÇÃO DE USUÁRIOS

### 1.1 Função de Login
\`\`\`typescript
const handleLogin = (e: React.FormEvent) => {
  e.preventDefault()
  if (loginData.username.trim()) {
    onLogin(loginData.username)
  }
}
\`\`\`

### 1.2 Função de Cadastro de Usuário
\`\`\`typescript
const handleRegister = (e: React.FormEvent) => {
  e.preventDefault()
  if (registerData.username.trim() && registerData.password === registerData.confirmPassword) {
    // Simular registro salvando no localStorage
    const users = JSON.parse(localStorage.getItem("inventory-users") || "[]")
    users.push({
      username: registerData.username,
      email: registerData.email,
      password: registerData.password,
    })
    localStorage.setItem("inventory-users", JSON.stringify(users))
    onLogin(registerData.username)
  }
}
\`\`\`

### 1.3 Função de Logout
\`\`\`typescript
const handleLogout = () => {
  localStorage.removeItem("inventory-user")
  setUser(null)
}
\`\`\`

### 1.4 Verificação de Usuário Logado
\`\`\`typescript
useEffect(() => {
  const savedUser = localStorage.getItem("inventory-user")
  if (savedUser) {
    setUser(savedUser)
  }
  setLoading(false)
}, [])
\`\`\`

---

## 2. GESTÃO DE PRODUTOS

### 2.1 Estrutura de Dados do Produto
\`\`\`typescript
interface Product {
  id: string
  name: string
  category: string
  quantity: number
  minStock: number
  price: number
  description: string
}
\`\`\`

### 2.2 Função de Adicionar Produto
\`\`\`typescript
const addProduct = (product: Omit<Product, "id">) => {
  const newProduct = {
    ...product,
    id: Date.now().toString(),
  }
  const newProducts = [...products, newProduct]
  saveProducts(newProducts)
  setShowAddForm(false)
}
\`\`\`

### 2.3 Função de Atualizar Produto
\`\`\`typescript
const updateProduct = (id: string, updatedProduct: Partial<Product>) => {
  const newProducts = products.map((p) => (p.id === id ? { ...p, ...updatedProduct } : p))
  saveProducts(newProducts)
}
\`\`\`

### 2.4 Função de Excluir Produto
\`\`\`typescript
const deleteProduct = (id: string) => {
  const newProducts = products.filter((p) => p.id !== id)
  saveProducts(newProducts)
}
\`\`\`

### 2.5 Função de Salvar Produtos no localStorage
\`\`\`typescript
const saveProducts = (newProducts: Product[]) => {
  setProducts(newProducts)
  localStorage.setItem("inventory-products", JSON.stringify(newProducts))
}
\`\`\`

### 2.6 Função de Carregar Produtos do localStorage
\`\`\`typescript
useEffect(() => {
  const savedProducts = localStorage.getItem("inventory-products")
  if (savedProducts) {
    setProducts(JSON.parse(savedProducts))
  }
}, [])
\`\`\`

---

## 3. SISTEMA DE BUSCA

### 3.1 Função de Filtrar Produtos
\`\`\`typescript
const filteredProducts = products.filter(
  (product) =>
    product.name.toLowerCase().includes(searchTerm.toLowerCase()) ||
    product.category.toLowerCase().includes(searchTerm.toLowerCase()),
)
\`\`\`

### 3.2 Campo de Busca com Estado
\`\`\`typescript
const [searchTerm, setSearchTerm] = useState("")

// Componente de busca
<Input
  placeholder="Buscar produtos..."
  value={searchTerm}
  onChange={(e) => setSearchTerm(e.target.value)}
  className="pl-10"
/>
\`\`\`

---

## 4. CÁLCULOS E MÉTRICAS

### 4.1 Função de Calcular Total de Produtos
\`\`\`typescript
const totalProducts = products.length
\`\`\`

### 4.2 Função de Calcular Valor Total do Estoque
\`\`\`typescript
const totalValue = products.reduce((sum, p) => sum + p.quantity * p.price, 0)
\`\`\`

### 4.3 Função de Detectar Produtos com Estoque Baixo
\`\`\`typescript
const lowStockProducts = products.filter((p) => p.quantity <= p.minStock).length
\`\`\`

### 4.4 Função de Formatação de Moeda
\`\`\`typescript
const formatCurrency = (value: number) => {
  return value.toLocaleString("pt-BR", { 
    style: 'currency', 
    currency: 'BRL',
    minimumFractionDigits: 2 
  })
}
\`\`\`

---

## 5. SISTEMA DE ALERTAS

### 5.1 Função de Verificar Estoque Baixo
\`\`\`typescript
const isLowStock = (product: Product) => {
  return product.quantity <= product.minStock
}
\`\`\`

### 5.2 Componente de Alerta Visual
\`\`\`typescript
{product.quantity <= product.minStock && (
  <Badge variant="destructive" className="text-xs">
    <AlertTriangle className="h-3 w-3 mr-1" />
    Estoque Baixo
  </Badge>
)}
\`\`\`

---

## 6. VALIDAÇÕES DE FORMULÁRIO

### 6.1 Validação de Campos Obrigatórios
\`\`\`typescript
const handleSubmit = (e: React.FormEvent) => {
  e.preventDefault()
  if (formData.name.trim() && formData.category) {
    onAdd(formData)
  }
}
\`\`\`

### 6.2 Validação de Números
\`\`\`typescript
onChange={(e) => setFormData({ 
  ...formData, 
  quantity: parseInt(e.target.value) || 0 
})}
\`\`\`

### 6.3 Validação de Preço
\`\`\`typescript
onChange={(e) => setFormData({ 
  ...formData, 
  price: parseFloat(e.target.value) || 0 
})}
\`\`\`

### 6.4 Validação de Confirmação de Senha
\`\`\`typescript
if (registerData.username.trim() && registerData.password === registerData.confirmPassword) {
  // Proceder com o cadastro
}
\`\`\`

---

## 7. EDIÇÃO INLINE DE PRODUTOS

### 7.1 Função de Iniciar Edição
\`\`\`typescript
const handleEdit = (product: Product) => {
  setEditingId(product.id)
  setEditData(product)
}
\`\`\`

### 7.2 Função de Salvar Edição
\`\`\`typescript
const handleSave = () => {
  if (editingId && editData) {
    onUpdate(editingId, editData)
    setEditingId(null)
    setEditData({})
  }
}
\`\`\`

### 7.3 Função de Cancelar Edição
\`\`\`typescript
const handleCancel = () => {
  setEditingId(null)
  setEditData({})
}
\`\`\`

---

## 8. CATEGORIAS DE PRODUTOS

### 8.1 Lista de Categorias Predefinidas
\`\`\`typescript
const categories = [
  "Eletrônicos",
  "Roupas",
  "Casa e Jardim",
  "Esportes",
  "Livros",
  "Alimentação",
  "Beleza",
  "Automóveis",
  "Outros",
]
\`\`\`

### 8.2 Componente de Seleção de Categoria
\`\`\`typescript
<Select
  value={formData.category}
  onValueChange={(value) => setFormData({ ...formData, category: value })}
>
  <SelectTrigger>
    <SelectValue placeholder="Selecione uma categoria" />
  </SelectTrigger>
  <SelectContent>
    {categories.map((category) => (
      <SelectItem key={category} value={category}>
        {category}
      </SelectItem>
    ))}
  </SelectContent>
</Select>
\`\`\`

---

## 9. CONFIRMAÇÃO DE EXCLUSÃO

### 9.1 Dialog de Confirmação
\`\`\`typescript
<AlertDialog>
  <AlertDialogTrigger asChild>
    <Button size="sm" variant="destructive">
      <Trash2 className="h-4 w-4" />
    </Button>
  </AlertDialogTrigger>
  <AlertDialogContent>
    <AlertDialogHeader>
      <AlertDialogTitle>Confirmar exclusão</AlertDialogTitle>
      <AlertDialogDescription>
        Tem certeza que deseja excluir o produto "{product.name}"? 
        Esta ação não pode ser desfeita.
      </AlertDialogDescription>
    </AlertDialogHeader>
    <AlertDialogFooter>
      <AlertDialogCancel>Cancelar</AlertDialogCancel>
      <AlertDialogAction onClick={() => onDelete(product.id)}>
        Excluir
      </AlertDialogAction>
    </AlertDialogFooter>
  </AlertDialogContent>
</AlertDialog>
\`\`\`

---

## 10. RESPONSIVIDADE E LAYOUT

### 10.1 Grid Responsivo para Cards
\`\`\`typescript
<div className="grid grid-cols-1 md:grid-cols-3 gap-6 mb-8">
  {/* Cards de métricas */}
</div>
\`\`\`

### 10.2 Layout Responsivo para Formulários
\`\`\`typescript
<div className="grid grid-cols-1 md:grid-cols-2 gap-4">
  {/* Campos do formulário */}
</div>
\`\`\`

### 10.3 Tabela Responsiva para Informações
\`\`\`typescript
<div className="grid grid-cols-2 md:grid-cols-4 gap-4 text-sm">
  {/* Informações do produto */}
</div>
\`\`\`

---

## 11. ESTADOS DE CARREGAMENTO

### 11.1 Loading State Inicial
\`\`\`typescript
const [loading, setLoading] = useState(true)

if (loading) {
  return (
    <div className="min-h-screen flex items-center justify-center">
      <div className="animate-spin rounded-full h-8 w-8 border-b-2 border-blue-600"></div>
    </div>
  )
}
\`\`\`

### 11.2 Estado Vazio de Produtos
\`\`\`typescript
if (products.length === 0) {
  return (
    <Card>
      <CardContent className="flex flex-col items-center justify-center py-12">
        <div className="text-gray-400 mb-4">
          <Search className="h-12 w-12" />
        </div>
        <h3 className="text-lg font-medium text-gray-900 mb-2">
          Nenhum produto cadastrado
        </h3>
        <p className="text-gray-500 text-center">
          Comece adicionando seu primeiro produto ao estoque.
        </p>
      </CardContent>
    </Card>
  )
}
\`\`\`

---

## 12. HOOKS PERSONALIZADOS

### 12.1 Hook de Gerenciamento de Estado Local
\`\`\`typescript
const [products, setProducts] = useState<Product[]>([])
const [showAddForm, setShowAddForm] = useState(false)
const [searchTerm, setSearchTerm] = useState("")
const [editingId, setEditingId] = useState<string | null>(null)
const [editData, setEditData] = useState<Partial<Product>>({})
\`\`\`

---

## 13. UTILITÁRIOS E HELPERS

### 13.1 Função de Geração de ID Único
\`\`\`typescript
const generateId = () => Date.now().toString()
\`\`\`

### 13.2 Função de Validação de Email
\`\`\`typescript
const isValidEmail = (email: string) => {
  const emailRegex = /^[^\s@]+@[^\s@]+\.[^\s@]+$/
  return emailRegex.test(email)
}
\`\`\`

### 13.3 Função de Formatação de Data
\`\`\`typescript
const formatDate = (timestamp: number) => {
  return new Date(timestamp).toLocaleDateString('pt-BR')
}
\`\`\`

---

## RESUMO

| Funcionalidade | Arquivo Principal | Linhas de Código | Complexidade |
|----------------|-------------------|------------------|--------------|
| Autenticação | login-form.tsx | ~150 | Média |
| Dashboard | dashboard.tsx | ~120 | Baixa |
| Gestão Produtos | add-product-form.tsx | ~180 | Média |
| Lista Produtos | product-list.tsx | ~250 | Alta |
| Busca/Filtros | product-list.tsx | ~30 | Baixa |
| Validações | Todos os arquivos | ~80 | Média |
| **TOTAL** | **4 arquivos** | **~810** | **Média** |

---

**© 2025 Estoque120394Teste - Documentação de Funcionalidades**
**Sistema desenvolvido com Next.js, React, TypeScript e Tailwind CSS**
