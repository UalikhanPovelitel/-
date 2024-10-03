<template>
  <div class="order-form">
    <h2>Оформление заказа</h2>
    <form @submit.prevent="submitOrder">
      <div class="mb-3">
        <label for="name" class="form-label">Имя:</label>
        <input type="text" id="name" class="form-control" v-model="name" required />
      </div>
      <div class="mb-3">
        <label for="email" class="form-label">Email:</label>
        <input type="email" id="email" class="form-control" v-model="email" required />
      </div>
      <div class="mb-3">
        <label for="address" class="form-label">Адрес доставки:</label>
        <input type="text" id="address" class="form-control" v-model="address" required />
      </div>
      <div class="mb-3">
        <label for="cardNumber" class="form-label">Номер карты:</label>
        <input type="text" id="cardNumber" class="form-control" v-model="cardNumber" required />
      </div>
      <div class="mb-3">
        <label for="expiryDate" class="form-label">Дата истечения (MM/YY):</label>
        <input type="text" id="expiryDate" class="form-control" v-model="expiryDate" placeholder="MM/YY" required />
      </div>
      <div class="mb-3">
        <label for="cvv" class="form-label">CVV:</label>
        <input type="text" id="cvv" class="form-control" v-model="cvv" required />
      </div>
      <button type="submit" class="btn btn-primary">Подтвердить заказ</button>
    </form>

    <div v-if="orderSubmitted" class="mt-3 alert alert-success">
      Заказ успешно оформлен!
    </div>
  </div>
</template>

<script>
export default {
  props: ['cart'], // Получаем корзину через пропс
  data() {
    return {
      name: '',
      email: '',
      address: '',
      cardNumber: '',
      expiryDate: '',
      cvv: '',
      orderSubmitted: false
    };
  },
  methods: {
    submitOrder() {
      // Убедитесь, что корзина не пуста перед отправкой
      if (this.cart.length === 0) {
        alert('Корзина пуста! Добавьте товары для оформления заказа.');
        return;
      }

      this.orderSubmitted = true;
      const orderDetails = {
        name: this.name,
        email: this.email,
        address: this.address,
        cardNumber: this.cardNumber,
        expiryDate: this.expiryDate,
        cvv: this.cvv,
        cart: this.cart // Добавляем информацию о товарах в заказ
      };
      console.log('Заказ оформлен:', orderDetails);
      this.$emit('submit-order', orderDetails); // Отправляем данные родительскому компоненту
    }
  }
}
</script>

<style scoped>
.order-form {
  max-width: 500px; /* Ограничиваем ширину формы */
  margin: auto; /* Центрируем форму */
}
</style>
