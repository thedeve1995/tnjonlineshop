<script setup>
import { RouterLink, RouterView } from 'vue-router'
import { useRouter } from "vue-router"

const router = useRouter()

const goToHome = () => {
  router.push("/")
}

const openMenu = () => {
  document.getElementById("nav").style.display = "flex";
  document.getElementById("menu").style.display = "none";
  document.getElementById("menu-close").style.display = "block";
}
const closeMenu = () => {
  document.getElementById("nav").style.display = "none";
  document.getElementById("menu-close").style.display = "none";
  document.getElementById("menu").style.display = "block";
}
</script>

<template>
  <header>
    <div class="wrapper">
      <img @click="goToHome()" src="./assets/logo2.png" height="60px" alt="">
      <span @click="openMenu()" class="menu" id="menu">Menu</span>
      <span @click="closeMenu()" class="menu-close" id="menu-close">X</span>
      <nav id="nav">
        <RouterLink to="/about">Pria</RouterLink>
        <RouterLink to="/about">Wanita</RouterLink>
        <RouterLink to="/about">Aksesoris</RouterLink>
        <RouterLink to="/about">Login</RouterLink>
        <RouterLink v-if="jikaLogin" to="/about">Keranjang</RouterLink>
      </nav>
    </div>
  </header>

  <RouterView class="view" />
  <footer>
  </footer>
</template>

<style scoped>
header{
  width: 100%;
  height: 9vh;
  margin-bottom: 10px;
}
header .wrapper{
  display: flex;
  padding: 5px 30px;
  justify-content: space-between;
  align-items: center;
}

header .wrapper img{
  cursor: pointer;
}

header .wrapper nav{
  display: flex;
  gap: 10px;
  justify-content: center;
  align-items: center;
}

header .wrapper nav a{
  text-decoration: none;
  font-weight: 700;
  color: rgb(245, 136, 20);
  transition: 0.3s;
}

footer{
  background-color: rgb(245, 136, 20);
  min-height: 50vh;
}
nav a.router-link-exact-active {
  background-color:rgb(245, 136, 20);
  color: white !important;
  padding: 2px 10px;
  border-radius: 5px;
}

nav a.router-link-exact-active:hover {

}

.menu{
  display: none;
}

.menu-close{
  display: none;
}

@media (max-width:500px) {
  .menu{
    display: block;
    font-weight: 700;
    color: rgb(245, 136, 20);
    transition: 0.5s;
  }

  header .wrapper{
    position: relative;
  }
  header .wrapper nav{
    position: absolute;
    background-color: rgba(255, 255, 255, 0.8);
    flex-direction: column;
    right: 0;
    top: 9vh;
    width: 100vw;
    height: 50vw;
    display: none;
    transition: 0.5s;
  }
}

</style>
