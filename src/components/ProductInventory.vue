<template>
  <div>
    <h1>Product Inventory</h1>
    <div class="upload-file-style">
      <input type="file" ref="file" @change="handleFileUpload">
    <button class="button" :class="{ 'button--primary': primary }" @click="handleUpdate">Update Inventory</button>
    </div>
    <table>
      <thead>
        <tr>
          <th>Product ID</th>
          <th>Type</th>
          <th>Brand</th>
          <th>Model</th>
          <th>Capacity</th>
          <th>Quantity</th>
        </tr>
      </thead>
      <tbody>
        <tr v-for="product in products" :key="product.id">
          <td>{{ product.product_id }}</td>
          <td>{{ product.type }}</td>
          <td>{{ product.brand }}</td>
          <td>{{ product.model }}</td>
          <td>{{ product.capacity }}</td>
          <td>{{ product.quantity }}</td>
        </tr>
      </tbody>
    </table>
  </div>
</template>

<script>
import axios from 'axios';

export default {
  data() {
    return {
      products: [],
      file: null,
    };
  },
  mounted() {
    this.fetchProducts();
  },
  methods: {
    fetchProducts() {
      axios.get('http://localhost:8000/api/product-master-list')
        .then(response => {
          this.products = response.data.data;
        })
        .catch(error => {
          console.log(error);
        });
    },
    handleFileUpload() {
      this.file = this.$refs.file.files[0];
    },
    handleUpdate() {
      if (!this.file) {
        alert('Please select a file to upload.');
        return;
      }

      const formData = new FormData();
      formData.append('file', this.file);
      formData.append('status', this.status);

      axios.post('http://localhost:8000/api/product-master-list/upload', formData, {
        headers: {
          'Content-Type': 'multipart/form-data',
        },
      })
        .then(response => {
          alert(response.data.message);
          this.fetchProducts();
        })
        .catch(error => {
          console.log(error);
        });
    },
  },
};
</script>

<style>
table {
  border-collapse: collapse;
  width: 100%;
}

th,
td {
  text-align: left;
  padding: 8px;
  border-bottom: 1px solid #ddd;
}

th {
  background-color: #f2f2f2;
}

.upload-file-style {
  padding-bottom: 10px;
}

.button {
  padding: 10px 20px;
  font-size: 16px;
  border-radius: 5px;
  border: none;
  cursor: pointer;
  color: #fff;
  background-color: #333;
  transition: background-color 0.2s ease;
}

.button:hover {
  background-color: #555;
}

.button--primary {
  background-color: #f00;
}
</style>
