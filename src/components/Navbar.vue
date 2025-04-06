<template>
    <nav class="navbar">
      <div class="logo">Mi App</div>
      <ul class="nav-links">
        <li><a href="#">Inicio</a></li>
        <li><a href="#">Buscar</a></li>
        <li><a href="#">Acerca de</a></li>
      </ul>
    </nav>
  </template>
  
  <script>
  export default {
    name: 'Navbar'
  }
  </script>
  
  <style scoped>
  .navbar {
    background-color: #42b983;
    color: white;
    display: flex;
    justify-content: space-between;
    align-items: center;
    padding: 1rem 2rem;
  }
  
  .logo {
    font-weight: bold;
    font-size: 1.5rem;
  }
  
  .nav-links {
    list-style: none;
    display: flex;
    gap: 1rem;
  }
  
  .nav-links li a {
    color: white;
    text-decoration: none;
    font-weight: bold;
  }
  
  .nav-links li a:hover {
    text-decoration: underline;
  }
  </style>
  