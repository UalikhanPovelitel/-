<template>
  <div class="product-list">
    <h1>Список тачек</h1>

    <!-- Фильтр по имени -->
    <label for="nameFilter">Фильтр по имени:</label>
    <select id="nameFilter" v-model="selectedName" class="custom-select">
      <option value="">Все</option>
      <option v-for="product in uniqueNames" :key="product" :value="product">{{ product }}</option>
    </select>

    <!-- Фильтр по категории -->
    <div class="filter-checkboxes">
      <label v-for="category in uniqueCategories" :key="category">
        <input type="checkbox" :value="category" v-model="selectedCategories" /> {{ category }}
      </label>
    </div>

    <!-- Сортировка -->
    <label for="sort">Сортировать по:</label>
    <select id="sort" v-model="sortOption" class="custom-select">
      <option value="priceAsc">Цена: по возрастанию</option>
      <option value="priceDesc">Цена: по убыванию</option>
      <option value="nameAsc">Имя: по алфавиту (A-Z)</option>
      <option value="nameDesc">Имя: по алфавиту (Z-A)</option>
    </select>

    <!-- Список отфильтрованных и отсортированных товаров -->
    <div class="products-container">
      <div v-for="product in sortedAndFilteredProducts" :key="product.id" 
           :class="['product mb-3', { 'highlighted': selectedProductIds.includes(product.id) }]">
        <img :src="product.imageUrl" alt="Изображение товара" class="product-image" />
        <h2>{{ product.name }}</h2>
        <p>Цена: {{ product.price }} ₽</p>
        <button @click="addToCart(product)" class="btn btn-lightning">
          ⚡
        </button>
      </div>
    </div>

    <!-- Корзина -->
    <h2>Корзина</h2>
    <div v-if="cart.length > 0" class="cart-container">
      <h3>Добавленные товары:</h3>
      <ul class="list-group">
        <li v-for="item in cart" :key="item.product.id" class="list-group-item d-flex justify-content-between align-items-center">
          <span>{{ item.product.name }} - {{ item.product.price }} ₽</span>
          <div class="quantity-controls">
            <button @click="decreaseQuantity(item)" class="btn btn-sm btn-outline-secondary">-</button>
            <span class="mx-2">{{ item.quantity }}</span>
            <button @click="increaseQuantity(item)" class="btn btn-sm btn-outline-secondary">+</button>
          </div>
        </li>
      </ul>
      <p class="mt-3"><strong>Итоговая сумма: {{ totalPrice }} ₽</strong></p>
      <button @click="checkout" class="btn btn-primary mt-3">Оформить заказ</button>
    </div>
    <div v-else>
      <p>Корзина пуста.</p>
    </div>

    <!-- Компонент для оформления заказа -->
    <OrderForm v-if="showOrderForm" :cart="cart" @submit-order="handleOrderSubmission" class="fade-in" />

    <!-- Сообщение об успешном оформлении заказа -->
    <div v-if="orderSuccess" class="alert alert-success mt-3 fade-in-out">
      Заказ успешно оформлен!
    </div>
  </div>
</template>

<script>
import OrderForm from '@/components/OrderForm.vue'; // Импортируем компонент OrderForm

export default {
  components: {
    OrderForm // Регистрируем компонент
  },
  data() {
    return {
      products: [
        { id: 1, name: 'Маквин', price: 100260, imageUrl: require('@/assets/mcvin.jpeg'), category: 'Спорт' },
        { id: 2, name: 'Метр', price: 2898700, imageUrl: require('@/assets/metr.jpeg'), category: 'Тягач' },
        { id: 3, name: 'Работяга', price: 3010, imageUrl: require('@/assets/franc.jpeg'), category: 'Работяга' },
        { id: 4, name: 'Хз', price: 15000, imageUrl: require('@/assets/car1.jpeg'), category: 'Спорт' },
        { id: 5, name: 'Метр +', price: 25000, imageUrl: require('@/assets/car2.jpeg'), category: 'Тягач' },
        { id: 6, name: 'Француа', price: 35000, imageUrl: require('@/assets/car3.jpeg'), category: 'Работяга' },
      ],
      selectedName: '',
      selectedCategories: [],
      sortOption: 'priceAsc',
      cart: [],
      orderSuccess: false,
      showOrderForm: false, // Новый флаг для отображения формы заказа
      selectedProductIds: [] // Инициализируем как пустой массив
    };
  },
  computed: {
    uniqueNames() {
      return [...new Set(this.products.map(product => product.name))];
    },
    uniqueCategories() {
      return [...new Set(this.products.map(product => product.category))];
    },
    filteredProducts() {
      let filtered = this.products;

      if (this.selectedName) {
        filtered = filtered.filter(product => product.name === this.selectedName);
      }

      if (this.selectedCategories.length > 0) {
        filtered = filtered.filter(product => this.selectedCategories.includes(product.category));
      }

      return filtered;
    },
    sortedAndFilteredProducts() {
      let sorted = [...this.filteredProducts];

      if (this.sortOption === 'priceAsc') {
        sorted.sort((a, b) => a.price - b.price);
      } else if (this.sortOption === 'priceDesc') {
        sorted.sort((a, b) => b.price - a.price);
      } else if (this.sortOption === 'nameAsc') {
        sorted.sort((a, b) => a.name.localeCompare(b.name));
      } else if (this.sortOption === 'nameDesc') {
        sorted.sort((a, b) => b.name.localeCompare(a.name));
      }

      return sorted;
    },
    totalPrice() {
      return this.cart.reduce((sum, item) => sum + item.product.price * item.quantity, 0);
    }
  },
  methods: {
    addToCart(product) {
      const cartItem = this.cart.find(item => item.product.id === product.id);

      if (cartItem) {
        cartItem.quantity += 1; // Увеличиваем количество, если товар уже в корзине
      } else {
        this.cart.push({ product, quantity: 1 }); // Добавляем новый товар с количеством 1
      }

      // Убедитесь, что мы добавляем в выделенные только уникальные значения
      if (!this.selectedProductIds.includes(product.id)) {
        this.selectedProductIds.push(product.id); // Добавляем товар в выделенные
      }
      
      console.log(`Товар добавлен в корзину: ${product.name}`);
    },

    increaseQuantity(item) {
      item.quantity += 1;
    },
    decreaseQuantity(item) {
      if (item.quantity > 1) {
        item.quantity -= 1;
      } else {
        this.removeFromCart(item);
      }
    },
    removeFromCart(item) {
      this.cart = this.cart.filter(cartItem => cartItem.product.id !== item.product.id);
      // Удаляем товар из выделенных
      this.selectedProductIds = this.selectedProductIds.filter(id => id !== item.product.id);
    },

    checkout() {
      console.log('Оформление заказа', this.cart);
      this.showOrderForm = true; // Показываем форму оформления заказа
    },
    handleOrderSubmission(orderDetails) {
      console.log('Детали заказа:', orderDetails);
      this.orderSuccess = true; // Устанавливаем флаг успешного оформления
      this.cart = []; // Очищаем корзину
      this.showOrderForm = false; // Скрываем форму оформления заказа
      this.selectedProductIds = []; // Очищаем выделенные товары
    }
  }
};
</script>

<style scoped>
.products-container {
  display: flex;
  flex-wrap: wrap;
  justify-content: flex-start;
}

.custom-select {
  padding: 5px; /* Уменьшаем отступы */
  width: 150px; /* Устанавливаем фиксированную ширину */
  height: 30px; /* Устанавливаем фиксированную высоту */
  border: 1px solid #0011ff; /* Синий бордер */
  border-radius: 5px;
  background-color: white;
  color: #0011ff; /* Синий текст */
  font-size: 14px;
  transition: border-color 0.1s; /* Плавный переход для цвета бордера */
}


.custom-select:hover {
  border-color: #007bff; /* Более светлый синий при наведении */
}

.fade-in {
  animation: fadeIn 0.5s forwards; /* Появление за 0.5 секунды */
}

@keyframes fadeIn {
  from {
    opacity: 0;
  }
  to {
    opacity: 1;
  }
}

.highlighted {
  background-color: rgba(255, 223, 186, 0.5); /* Светлый фон */
  border: 2px solid #ffcc00; /* Золотая рамка */
  box-shadow: 0 0 10px rgba(255, 204, 0, 0.7); /* Эффект свечения */
  transform: scale(1.05); /* Немного увеличиваем размер */
  transition: transform 0.2s, box-shadow 0.2s; /* Плавный переход */
}

.product {
  border: 1px solid #0011ff;
  border-radius: 5px;
  padding: 15px;
  margin: 10px;
  background-color: white;
  transition: box-shadow 0.3s;
  width: 180px;
  opacity: 0;
  transform: translateY(20px);
  animation: fadeIn 0.5s forwards;
}

.product:hover {
  box-shadow: 0 4px 8px rgba(0, 0, 0, 0.2);
}

.product-image {
  width: 100%;
  height: auto;
}
</style>
