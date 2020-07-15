<template>
  <div class="container">
    <div>
      <h1>Productos</h1>
    </div>

    <div>
      <div class="text-left pull-left">
        <label for="">Buscar</label>
        <input label="Buscar..." v-model="filtro" />
        <b-button> <b-icon-arrow-right-circle-fill></b-icon-arrow-right-circle-fill> </b-button>
      </div>

      <div class="text-right pull-right">
        <span class="stats">
          {{ cart.items.length }}
          <template v-if="cart.items.length == 1">item</template>
          <template v-else>productos</template>
          en el carro, subtotal {{ cartTotal | currency }}
          <b-button class="btn btn-success" @click="isShowingCart = true" v-b-modal.modal-1> <b-icon-cart></b-icon-cart> {{ cart.items.length }} View Cart</b-button>
        </span>
      </div>
    </div>

    <div class="container" fluid="sm">
      <b-row>
        <b-col cols="12" sm="6" md="4" xl="3" class="py-5" v-for="(producto, index) in productos" :key="index">
          <b-card style="max-width: 350px;">
            <img style="max-width: 10rem;" class="mb-2" :src="producto.image" alt="" />
            <b-card-title>
              {{ producto.name }}
            </b-card-title>
            <b-card-text> {{ producto.description }} </b-card-text>
            <b-card-text class="h4">
              <strong>{{ producto.price | currency }}</strong>
            </b-card-text>

            <p>{{ producto.inStock }} en stock</p>

            <b-button href="#" variant="success" @click="addProductToCart(producto)" :disabled="producto.inStock == 0">Agregar a carro</b-button>
          </b-card>
        </b-col>
      </b-row>
    </div>

    <b-modal id="modal-1" ref="modal-1" hide-footer title="Carro de Compra">
      <table v-if="cart.items.length > 0" class="table table-striped">
        <thead>
          <tr>
            <th>Producto</th>
            <th>Cantidad</th>
            <th>Precio</th>
          </tr>
        </thead>
        <tbody>
          <tr v-for="(item, index) in cart.items" :key="index">
            <td>{{ item.product.name }}</td>
            <td>
              {{ item.quantity }} &nbsp;
              <b-button variant="outline-success" sm @click="increaseQuantity(item)" :disabled="item.product.inStock == 0">+</b-button>
              <b-button variant="outline-danger" @click="decreaseQuantity(item)">-</b-button>
            </td>
            <td>{{ (item.quantity * item.product.price) | currency }}</td>
          </tr>

          <tr>
            <td class="text-right" colspan="2">
              <strong>Subtotal</strong>
            </td>

            <td>{{ cartTotal | currency }}</td>
          </tr>

          <tr>
            <td class="text-right" colspan="2">
              <strong>IVA</strong>
            </td>

            <td>{{ taxAmount | currency }}</td>
          </tr>

          <tr>
            <td class="text-right" colspan="2">
              <strong>Total</strong>
            </td>

            <td>{{ (cartTotal + taxAmount) | currency }}</td>
          </tr>

          <tr>
            <td colspan="2"></td>
            <td><b-button pill variant="success" @click="checkout">Checkout</b-button></td>
          </tr>
        </tbody>
      </table>

      <p v-else>Su carrito está vacío.</p>

      <b-button class="mt-3" squared variant="outline-danger" block @click="hideModal">Cerrar</b-button>
    </b-modal>
  </div>
</template>

<script>
import Navbar from "../components/Navbar";
export default {
  components: {
    Navbar,
  },
  data() {
    return {
      cart: {
        items: [],
      },
      name: [],
      arrayFiltrado: [],
      productos: [
        {
          id: 1,
          name: "iPad Pro (9.7 inch)",
          description: "We heard it's supposed to be pretty good. At least that's what people say.",
          price: 500,
          image: "https://www.techinn.com/f/13734/137343538/apple-ipad-pro-11-1tb.jpg",
          inStock: 10,
        },
        {
          id: 2,
          name: "OnePlus 3 cover",
          description: "Does your phone spend most of its time on the ground? This cheap piece of plastic is the solution!",
          price: 1000,
          image: "https://www.oppomart.com/media/catalog/product/cache/4/image/9df78eab33525d08d6e5fb8d27136e95/o/p/op3c.png",
          inStock: 11,
        },
        {
          id: 3,
          name: "iPad Pro (9.7 inch)",
          description: "We heard it's supposed to be pretty good. At least that's what people say.",
          price: 500,
          image: "https://www.techinn.com/f/13734/137343538/apple-ipad-pro-11-1tb.jpg",
          inStock: 10,
        },
        {
          id: 4,
          name: "iPad Pro (9.7 inch)",
          description: "We heard it's supposed to be pretty good. At least that's what people say.",
          price: 500,
          image: "https://www.techinn.com/f/13734/137343538/apple-ipad-pro-11-1tb.jpg",
          inStock: 10,
        },
        {
          id: 5,
          name: "iPad Pro (9.7 inch)",
          description: "We heard it's supposed to be pretty good. At least that's what people say.",
          price: 500,
          image: "https://www.techinn.com/f/13734/137343538/apple-ipad-pro-11-1tb.jpg",
          inStock: 10,
        },
        {
          id: 6,
          name: "iPad Pro (9.7 inch)",
          description: "We heard it's supposed to be pretty good. At least that's what people say.",
          price: 500,
          image: "https://www.techinn.com/f/13734/137343538/apple-ipad-pro-11-1tb.jpg",
          inStock: 10,
        },
        {
          id: 7,
          name: "iPad Pro (9.7 inch)",
          description: "We heard it's supposed to be pretty good. At least that's what people say.",
          price: 500,
          image: "https://www.techinn.com/f/13734/137343538/apple-ipad-pro-11-1tb.jpg",
          inStock: 10,
        },
      ],
    };
  },
  computed: {
    filtro: {
      get() {
        return this.name;
      },
      set(value) {
        value = value.toLowerCase();
        this.arrayFiltrado = this.productos.filter((productos) => productos.name.toLowerCase().indexOf(value) !== -1);
        this.name = value;
      },
    },
    cartTotal: function() {
      var total = 0;
      this.cart.items.forEach(function(item) {
        total += item.quantity * item.product.price;
      });
      return total;
    },
    taxAmount: function() {
      return (this.cartTotal * 19) / 100;
    },
  },
  filters: {
    currency: function(value) {
      var formatter = new Intl.NumberFormat("es-CL", {
        style: "currency",
        currency: "CLP",
        minimumFractionDigits: 0,
      });
      return formatter.format(value);
    },
  },
  methods: {
    hideModal() {
      this.$refs["modal-1"].hide();
    },
    removeItemFromCart: function(cartItem) {
      var index = this.cart.items.indexOf(cartItem);

      if (index !== -1) {
        this.cart.items.splice(index, 1);
      }
    },
    checkout: function() {
      if (confirm("Estás seguro de que quieres comprar estos productos?")) {
        this.cart.items.forEach(function(item) {
          item.product.inStock += item.quantity;
        });

        this.cart.items = [];
      }
    },
    addProductToCart: function(product) {
      var cartItem = this.getCartItem(product);

      if (cartItem != null) {
        cartItem.quantity++;
      } else {
        this.cart.items.push({
          product: product,
          quantity: 1,
        });
      }
      product.inStock--;
    },
    increaseQuantity: function(cartItem) {
      cartItem.product.inStock--;
      cartItem.quantity++;
    },
    decreaseQuantity: function(cartItem) {
      cartItem.quantity--;
      cartItem.product.inStock++;
      if (cartItem.quantity == 0) {
        this.removeItemFromCart(cartItem);
      }
    },
    getCartItem: function(product) {
      for (var i = 0; i < this.cart.items.length; i++) {
        if (this.cart.items[i].product.id === product.id) {
          return this.cart.items[i];
        }
      }

      return null;
    },
  },
};
</script>
