* {
        box-sizing: border-box;
        margin: 0;
        padding: 0;
        font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
    }

    body {
        background-color: var(--bg-color);
        color: var(--text-color);
    }

    header {
        background-color: var(--dark-color);
        color: white;
        padding: 1.2rem 2rem;
        display: flex;
        justify-content: space-between;
        align-items: center;
        box-shadow: 0 2px 10px rgba(0,0,0,0.1);
    }

    header h1 {
        font-size: 1.5rem;
        display: flex;
        align-items: center;
        gap: 10px;
    }

    .cart-indicator {
        background-color: var(--primary-color);
        padding: 0.5rem 1rem;
        border-radius: 20px;
        font-weight: bold;
        display: flex;
        align-items: center;
        gap: 8px;
        transition: transform 0.2s;
    }
    
    /* Animação simples quando um item é adicionado */
    .cart-indicator.bump {
        transform: scale(1.1);
    }

    .container {
        max-width: 1000px;
        margin: 2rem auto;
        padding: 0 1rem;
    }

    .search-bar {
        width: 100%;
        padding: 1rem;
        font-size: 1.1rem;
        border: 2px solid #ddd;
        border-radius: 8px;
        margin-bottom: 2rem;
        outline: none;
        transition: border-color 0.3s;
    }

    .search-bar:focus {
        border-color: var(--primary-color);
    }

    .product-grid {
        display: grid;
        grid-template-columns: repeat(auto-fill, minmax(250px, 1fr));
        gap: 2rem;
    }

    /* Estilos do Componente ProductCard */
    .product-card {
        background: white;
        border-radius: 10px;
        padding: 1.5rem;
        text-align: center;
        box-shadow: 0 4px 6px rgba(0,0,0,0.05);
        transition: transform 0.2s, box-shadow 0.2s;
    }

    .product-card:hover {
        transform: translateY(-5px);
        box-shadow: 0 8px 15px rgba(0,0,0,0.1);
    }

    .product-image {
        width: 100%;
        height: 180px;
        object-fit: cover;
        border-radius: 6px;
        margin-bottom: 1rem;
        background-color: #eee;
    }

    .product-title {
        font-size: 1.2rem;
        margin-bottom: 0.5rem;
        color: var(--dark-color);
    }

    .product-price {
        font-size: 1.3rem;
        font-weight: bold;
        color: var(--primary-color);
        margin-bottom: 1.2rem;
    }

    .btn-add {
        background-color: var(--primary-color);
        color: white;
        border: none;
        padding: 0.8rem 1.5rem;
        border-radius: 5px;
        font-size: 1rem;
        cursor: pointer;
        width: 100%;
        font-weight: bold;
        transition: background-color 0.3s;
    }

    .btn-add:hover {
        background-color: #3aa876;
    }

    .no-results {
        text-align: center;
        grid-column: 1 / -1;
        padding: 3rem;
        color: #777;
        font-size: 1.2rem;
    }
</style>


<!-- Ponto de montagem da aplicação Vue -->
<div id="app">
    <header>
        <h1>
            <svg width="24" height="24" viewBox="0 0 196.32 170.02" xmlns="http://www.w3.org/2000/svg">
                <path fill="#42b883" d="M120.83 0L98.16 39.26 75.5 0H0l98.16 170.02L196.32 0h-75.49z"/>
                <path fill="#35495e" d="M120.83 0L98.16 39.26 75.5 0H39.26l58.9 102.01L157.06 0h-36.23z"/>
            </svg>
            Vue Store
        </h1>
        <!-- Reatividade: O contador atualiza instantaneamente -->
        <div :class="['cart-indicator', { 'bump': bumpCart }]">
            🛒 Itens no carrinho: {{ cartCount }}
        </div>
    </header>

    <main class="container">
        <!-- Interatividade Simples: Data Bind com v-model -->
        <input 
            type="text" 
            v-model="searchQuery" 
            class="search-bar" 
            placeholder="Pesquisar produtos (ex: Notebook, Teclado...)"
        >

        <div class="product-grid">
            <!-- Iterando sobre os produtos filtrados -->
            <product-card 
                v-for="product in filteredProducts" 
                :key="product.id"
                :nome="product.name"
                :preco="product.price"
                :imagem="product.image"
                @adicionar="handleAddToCart"
            ></product-card>
            
            <div v-if="filteredProducts.length === 0" class="no-results">
                Nenhum produto encontrado para "{{ searchQuery }}".
            </div>
        </div>
    </main>
</div>

<script>
    const { createApp } = Vue;

    // 1. Criação do Componente de Card de Produto
    const ProductCard = {
        // Recebendo props conforme exigido
        props: ['nome', 'preco', 'imagem'],
        template: `
            <div class="product-card">
                <img :src="imagem" :alt="nome" class="product-image">
                <h3 class="product-title">{{ nome }}</h3>
                <p class="product-price">R$ {{ preco.toFixed(2).replace('.', ',') }}</p>
                <button class="btn-add" @click="addToCart">
                    Adicionar ao Carrinho
                </button>
            </div>
        `,
        methods: {
            addToCart() {

                this.$emit('adicionar');
            }
        }
    };
    const app = createApp({
        components: {
            'product-card': ProductCard
        },
        data() {
            return {
                cartCount: 0,
                searchQuery: '',
                bumpCart: false, 
                products: [
                    { id: 1, name: 'Notebook Pro 15"', price: 4500.00, image: 'https://images.unsplash.com/photo-1496181133206-80ce9b88a853?auto=format&fit=crop&w=500&q=60' },
                    { id: 2, name: 'Teclado Mecânico RGB', price: 350.50, image: 'https://images.unsplash.com/photo-1595225476474-87563907a212?auto=format&fit=crop&w=500&q=60' },
                    { id: 3, name: 'Mouse Sem Fio Ultra', price: 180.00, image: 'https://images.unsplash.com/photo-1527864550417-7fd91fc51a46?auto=format&fit=crop&w=500&q=60' },
                    { id: 4, name: 'Monitor Ultrawide 29"', price: 1250.00, image: 'https://images.unsplash.com/photo-1527443224154-c4a3942d3acf?auto=format&fit=crop&w=500&q=60' },
                    { id: 5, name: 'Headset Gamer 7.1', price: 299.90, image: 'https://images.unsplash.com/photo-1618366712010-f4ae9c647dcb?auto=format&fit=crop&w=500&q=60' },
                    { id: 6, name: 'Cadeira Ergonômica', price: 890.00, image: 'https://images.unsplash.com/photo-1505843490538-5133c6c7d0e1?auto=format&fit=crop&w=500&q=60' }
                ]
            };
        },
        computed: {
            filteredProducts() {
                const query = this.searchQuery.toLowerCase().trim();
                if (!query) return this.products;
                return this.products.filter(product => 
                    product.name.toLowerCase().includes(query)
                );
            }
        },
        methods: {
            handleAddToCart() {
                this.cartCount++;
                // Dispara a animação da UI
                this.bumpCart = true;
                setTimeout(() => {
                    this.bumpCart = false;
                }, 200);
            }
        }
    });
    app.mount('#app');
</script>
