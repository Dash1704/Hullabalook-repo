<template>
  <div class="app" ref="app-container">
    <div class="shop-title">
      <h2>Dash's Trainer Shop</h2>
    </div>
    
    <div class="filters">
      <h3>Filters</h3>
      <label class="toggle-switch">
        <input type="checkbox" v-model="showAvailable" />
        <span class="slider round"></span>
      </label>
      Show Available Products

      <div class="brand-filters">
        <label v-for="brand in uniqueBrands" :key="brand">
          <input type="checkbox" v-model="selectedBrands" :value="brand" />
          {{ brand }}
        </label>
      </div>

      <div class="product-dropdown">
        <label for="sort">Sort by:</label>
        <select id="sort-dropdown" v-model="sortOrder">
          <option value="asc">Lowest - Highest (£)</option>
          <option value="desc">Highest - Lowest (£)</option>
          <option value="rank_asc">Relevance: Ascending Rank</option>
        </select>
      </div>
    </div>

    <div class="product-count">
      <p>Number of Results: {{ filteredProducts.length }}</p>
    </div>

    <div class="product-grid">
      <ProductGridItem
        v-for="(product, index) in sortedProducts"
        :key="index"
        :product="product"
      />
    </div>
  </div>
</template>

<script>
import ProductGridItem from './components/ProductGridItem.vue';
import products from './data/products.json';

export default {
  name: 'App',
  components: {
    ProductGridItem,
  },
  data() {
    return {
      products,
      showAvailable: false,
      selectedBrands: [],
      sortOrder: 'asc',
    };
  },
  computed: {
    filteredProducts() {
      if (this.showAvailable) {
        return this.products.filter(product => {
          return (
            product.isAvailable &&
            (this.selectedBrands.length === 0 ||
              this.selectedBrands.includes(product.brand))
          );
        });
      } else {
        return this.products.filter(product => {
          return (
            this.selectedBrands.length === 0 ||
            this.selectedBrands.includes(product.brand)
          );
        });
      }
    },
    sortedProducts() {
      const sortedProducts = this.filteredProducts.slice();
      if (this.sortOrder === 'asc') {
        sortedProducts.sort((a, b) => a.price - b.price);
      } else if (this.sortOrder === 'desc') {
        sortedProducts.sort((a, b) => b.price - a.price);
      } else if (this.sortOrder === 'rank_asc') {
        sortedProducts.sort((a, b) => {
            if (a.isAvailable === b.isAvailable) {
              if (a.rank === null) return 1; 
              if (b.rank === null) return -1; 
             return a.rank - b.rank;
            } else if (a.isAvailable) {
                return -1;
            } else {
                return 1;
              }
            });
      }
      return sortedProducts;
    },
    uniqueBrands() {
      return [...new Set(this.products.map(product => product.brand))];
    },
  },
};
</script>

<style>
.app {
  max-width: 1024px;
  margin: 0 auto;
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  color: #606569;
}

.product-grid {
  display: flex;
  flex-wrap: wrap;
  gap: 20px;
}

.link {
  color: #3a7f71;
}

.brand-filters {
  margin-top: 10px;
}

.product-dropdown {
  margin-top: 10px;
}

.product-count {
  margin-top: 10px;
}

.product-sort label {
  margin-right: 10px; 
}

.toggle-switch {
  position: relative;
  display: inline-block;
  width: 52px;
  height: 26px;
  margin-bottom: 5px;
  margin-right: 5px;
}

.toggle-switch input {
  opacity: 0;
  width: 0;
  height: 0;
}

.slider {
  position: absolute;
  cursor: pointer;
  top: 0;
  left: 0;
  right: 0;
  bottom: 0;
  background-color: #ccc;
  -webkit-transition: 0.4s;
  transition: 0.4s;
  border-radius: 34px;
}

.slider:before {
  position: absolute;
  content: "";
  height: 18px;
  width: 18px;
  left: 4px;
  bottom: 4px;
  background-color: white;
  -webkit-transition: 0.4s;
  transition: 0.4s;
  border-radius: 50%;
}

input:checked + .slider {
  background-color: blue;
}

input:checked + .slider:before {
  -webkit-transform: translateX(26px);
  -ms-transform: translateX(26px);
  transform: translateX(26px);
}

input:disabled + .slider {
  background-color: #ccc;
  cursor: not-allowed;
}

input:disabled + .slider:before {
  background-color: #ccc;
}

#sort-dropdown {
  margin-left: 5px;
  padding: 10px; 
  border: 1px solid #ccc; 
  border-radius: 5px; 
  background-color: #fff;
  color: #333;
  font-size: 13px; 
  cursor: pointer; 
}
</style>
