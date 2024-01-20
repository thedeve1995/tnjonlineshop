<template>
  <div class="cart">
    <h1>You have {{ userCart.length }} Item in your cart</h1>
    <div class="total">
      <p>Your Total Price : Rp {{ formatCurrency(totalCart) }}</p>

      <button>Kirim Barang</button>
    </div>
    <div class="cartItems">
      <div class="item" v-for="cart in userCart">
        <div class="img">
          <img width="60" :src="cart.linkImage[0]" alt="gambar">
        </div>
        <div class="ket">
          <h3>{{ cart.namaBarang }}</h3>
          <p>Harga {{ formatCurrency(cart.harga) }}</p>
          Jumlah <input style="padding: 0 10px;min-width: 30px;max-width: 50px; text-align: center;border-radius: 4px;"
            v-model="cart.quantity" @input="updateQuantity(cart.id, $event.target.value)" type="number">

        </div>
      </div>

    </div>
    <form @submit.prevent="kirimBarang">
      <input type="text" v-model="namaPembeli" placeholder="Nama Pembeli">
      <input type="text" v-model="alamatRumahPembeli" placeholder="Alamat">
      <input type="number" v-model="nomorWaPembeli" placeholder="Nomor WA">
      <input type="submit" value="Kirim Barang ">
    </form>
  </div>
</template>

<script setup>
import { ref, onMounted } from 'vue';
import { getAuth, onAuthStateChanged, signOut } from "firebase/auth";
import {
  doc, updateDoc, getDoc
} from 'firebase/firestore'
import { db } from '@/firebase'
import { useRouter } from "vue-router"

onMounted(async () => {

  onAuthStateChanged(auth, (user) => {
    if (user) {
      isLoggedIn.value = true;
      const email = user.email
      emailUser = email;
      loadCart();
    } else {
      isLoggedIn.value = false;
    }
  });
});

const userCart = ref([])
const auth = getAuth();
const isLoggedIn = ref(false);
let totalCart

const namaPembeli = ref("");
const alamatRumahPembeli = ref("")
const nomorWaPembeli = ref("")

const kirimBarang = () => {
  const formattedCart = userCart.value.map(cartItem => ({
    namaBarang: cartItem.namaBarang,
    Harga: formatCurrency(cartItem.harga),
    quantity: cartItem.quantity
  }));

  const totalFormatted = formatCurrency(totalCart);

  const message = `
Pesanan Baru
Nama: ${namaPembeli.value}
Alamat: ${alamatRumahPembeli.value}
Nomor WA: ${nomorWaPembeli.value}
Barang Yang dipesan: 
${formattedCart.map((item, index) => `
  ${index + 1}. Nama Barang: ${item.namaBarang}
     Jumlah: ${item.quantity}
     Harga Satuan: ${item.Harga}`
  ).join('\n')}
  
Total yang perlu dibayar: ${totalFormatted}`;

  const encodedMessage = encodeURIComponent(message);

  window.open(`http://wa.me/6287787045257?text=${encodedMessage}`);
};

let emailUser

const formatCurrency = (value) => {
  return new Intl.NumberFormat('id-ID', { style: 'currency', currency: 'IDR' }).format(value);
};

const loadCart = async () => {
  if (emailUser) {
    const userRef = doc(db, 'user', emailUser);

    try {
      const docSnap = await getDoc(userRef);

      if (docSnap.exists()) {
        const data = docSnap.data();
        if (data.cart) {
          userCart.value = data.cart;
          countTotalCart();
        }
      } else {
        console.log('No such document!');
      }
    } catch (e) {
      console.error('Error getting document:', e);
    }
  }
};

const updateCartQuantity = async (cartItemId, newQuantity) => {
  const userRef = doc(db, 'user', emailUser);

  try {
    const docSnap = await getDoc(userRef);

    if (docSnap.exists()) {
      const data = docSnap.data();
      if (data.cart) {
        const updatedCart = data.cart.map((cartItem) => {
          if (cartItem.id === cartItemId) {
            cartItem.quantity = newQuantity;
          }
          return cartItem;
        });

        await updateDoc(userRef, { cart: updatedCart });
        loadCart();
      }
    }
  } catch (e) {
    console.error('Error updating document:', e);
  }
};

const updateQuantity = (cartItemId, newQuantity) => {
  const parsedQuantity = parseInt(newQuantity, 10);
  if (!isNaN(parsedQuantity) && parsedQuantity >= 0) {
    updateCartQuantity(cartItemId, parsedQuantity);
  }
};

const countTotalCart = () => {
  totalCart = userCart.value.reduce((total, cartItem) => {
    return total + cartItem.quantity * cartItem.harga;
  }, 0);
};



</script>

<style scoped>
.cart {
  min-height: 90vh;
  display: flex;
  flex-direction: column;
  align-items: center;
  gap: 10px;

}

.cart h1 {
  text-align: center;
}

.cartItems {
  height: 70vh;
  border: 1px solid #f58814;
  width: 50vw;
  border-radius: 10px;
  padding: 20px;
  box-sizing: border-box;
  display: flex;
  flex-direction: column;
  gap: 10px;
  margin-bottom: 20px;
  overflow-y: scroll;
}

.item {
  display: flex;
  width: 100%;
  background-color: #f58814;
  padding: 10px;
  box-sizing: border-box;
  border-radius: 5px;
  justify-content: flex-start;
  color: white;
  gap: 10px;
  align-items: center;
}

.total {
  display: flex;
  width: 30vw;
  justify-content: space-around;
  align-items: center;
}

.total button {
  padding: 5px 20px;
}
</style>