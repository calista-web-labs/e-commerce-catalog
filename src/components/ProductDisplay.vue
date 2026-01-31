<template>
  <div class="container" :class="pageClass">
    
    <div v-if="loading" class="card">
      <div class="loader"></div>
    </div>

    <div v-else-if="product && !isUnavailable" class="card product-container">
      <div class="left-column">
        <img :src="product.image" :alt="product.title" class="product-img">
      </div>
      
      <div class="right-column">
        <h2 class="title" :class="textClass">{{ product.title }}</h2>
        
        <div class="sub-info">
          <span>{{ product.category }}</span>
          <div class="rating">
            <span>{{ product.rating.rate }}/5</span>
            <div class="circles">
              <span 
                v-for="n in 5" :key="n" 
                class="circle" 
                :class="[
                  n <= Math.round(product.rating.rate) ? 'filled' : '',
                  product.category === 'men\'s clothing' ? 'bg-men' : 'bg-women'
                ]"
              ></span>
            </div>
          </div>
        </div>

        <hr>
        <p class="description">{{ product.description }}</p>
        <hr>
        
        <div class="price" :class="textClass">${{ product.price }}</div>

        <div class="cta-buttons">
          <button class="btn-buy" :class="buttonClass">Buy now</button>
          <button class="btn-next" :class="borderClass" @click="getNextProduct">Next product</button>
        </div>
      </div>
    </div>

    <div v-else class="card unavailable-container">
      <div class="overlay">
        <p>This product is unavailable to show</p>
        <button class="btn-next-outline" @click="getNextProduct">Next product</button>
      </div>
    </div>

  </div>
</template>

<script>
export default {
  name: 'ProductDisplay',
  data() {
    return {
      index: 1,           // Index mulai dari 1 (Instructions Point 1)
      product: null,      // Variable untuk simpan data
      loading: false,
      isUnavailable: false // Penanda status unavailable
    }
  },
  computed: {
    // Menentukan Background (Biru/Pink/Abu)
    pageClass() {
      if (this.loading) return 'bg-loader';
      if (this.isUnavailable) return 'bg-unavailable'; // Abu-abu
      if (this.product?.category === "men's clothing") return 'bg-men-section';
      if (this.product?.category === "women's clothing") return 'bg-women-section';
      return 'bg-unavailable';
    },
    // Menentukan Warna Text (Biru/Pink)
    textClass() {
      return this.product?.category === "men's clothing" ? 'text-men' : 'text-women';
    },
    // Menentukan Warna Tombol Buy
    buttonClass() {
      return this.product?.category === "men's clothing" ? 'btn-men' : 'btn-women';
    },
    // Menentukan Warna Border Tombol Next
    borderClass() {
      return this.product?.category === "men's clothing" ? 'border-men' : 'border-women';
    }
  },
  methods: {
    async fetchProduct() {
      this.loading = true;
      this.product = null; // Reset dulu biar bersih
      this.isUnavailable = false;

      try {
        const response = await fetch(`https://fakestoreapi.com/products/${this.index}`);
        const data = await response.json();

        // Cek jika kategori Men atau Women
        if (data.category === "men's clothing" || data.category === "women's clothing") {
          this.product = data; // Simpan balasan
          this.isUnavailable = false;
        } else {
          this.isUnavailable = true; // Jangan simpan sebagai produk valid
        }
      } catch (error) {
        console.error(error);
        this.isUnavailable = true;
      } finally {
        this.loading = false;
      }
    },
    getNextProduct() {
      // Logic Index (Instructions Point 2 & 1)
      this.index++;
      if (this.index > 20) {
        this.index = 1; // reset ke 1 jika lebih dari 20
      }
      this.fetchProduct();
    }
  },
  mounted() {
    this.fetchProduct();
  }
}
</script>