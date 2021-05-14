<template>
  <div id="app">

    <!-- Insertamos el componente que inserta la grafica y los dropdowns de seleeción. Ademas pasamos los datos necesarios para
    graficarla. -->
    <BarChart xKey="nombre" yKey="IDH" xKeyMob = "abreviatura" :data="barChartData"/>

  </div>
</template>

<script>

// Importamos el componente hijo 
import BarChart from "./components/BarChart.vue";

// Creamos una función para generar un IDH [0,1], la función Math.random() solo genera numeros en el rango de [0,1). 
function randomIDH() {

  let IDH = 0

  IDH = Math.random() * 1.01

  //Si el IDH es mayor o igual a 1, retornamos 1, si no, retornamos el IDH generado.
  if (IDH >= 1) {

    IDH = 1
    return IDH

  } else {

    return IDH
  }
}


export default {
  name: "App",
  components: {
    BarChart
  },
  data: () => ({

    // Datos a utilizar para realizar la grafica
    barChartData: [
      { nombre: "Aguascalientes", abreviatura: "Ags.", IDH: [randomIDH(),randomIDH(),randomIDH(),randomIDH(),randomIDH()] },
      { nombre: "Baja California", abreviatura: "B.C.", IDH: [randomIDH(),randomIDH(),randomIDH(),randomIDH(),randomIDH()] },
      { nombre: "Baja California Sur", abreviatura: "B.C.S.", IDH: [randomIDH(),randomIDH(),randomIDH(),randomIDH(),randomIDH()] },
      { nombre: "Campeche", abreviatura: "Camp.", IDH: [randomIDH(),randomIDH(),randomIDH(),randomIDH(),randomIDH()] },
      { nombre: "Cd. de México", abreviatura: "CDMX", IDH: [randomIDH(),randomIDH(),randomIDH(),randomIDH(),randomIDH()] },
      { nombre: "Chiapas", abreviatura: "Chis", IDH: [randomIDH(),randomIDH(),randomIDH(),randomIDH(),randomIDH()] },
      { nombre: "Chihuahua", abreviatura: "Chih.", IDH: [randomIDH(),randomIDH(),randomIDH(),randomIDH(),randomIDH()] },
      { nombre: "Coahuila", abreviatura: "Coah.", IDH: [randomIDH(),randomIDH(),randomIDH(),randomIDH(),randomIDH()] },
      { nombre: "Colima", abreviatura: "Col.", IDH: [randomIDH(),randomIDH(),randomIDH(),randomIDH(),randomIDH()] },
      { nombre: "Durango", abreviatura: "Dgo.", IDH: [randomIDH(),randomIDH(),randomIDH(),randomIDH(),randomIDH()] },
      { nombre: "Guanajuato", abreviatura: "Gto.", IDH: [randomIDH(),randomIDH(),randomIDH(),randomIDH(),randomIDH()] },
      { nombre: "Guerrero", abreviatura: "Gro.", IDH: [randomIDH(),randomIDH(),randomIDH(),randomIDH(),randomIDH()] },
      { nombre: "Hidalgo", abreviatura: "Hgo.", IDH: [randomIDH(),randomIDH(),randomIDH(),randomIDH(),randomIDH()] },
      { nombre: "Jalisco", abreviatura: "Jal.", IDH: [randomIDH(),randomIDH(),randomIDH(),randomIDH(),randomIDH()] },
      { nombre: "México", abreviatura: "Mex.", IDH: [randomIDH(),randomIDH(),randomIDH(),randomIDH(),randomIDH()] },
      { nombre: "Michoacán", abreviatura: "Mich.", IDH: [randomIDH(),randomIDH(),randomIDH(),randomIDH(),randomIDH()] },
      { nombre: "Morelos", abreviatura: "Mor.", IDH: [randomIDH(),randomIDH(),randomIDH(),randomIDH(),randomIDH()] },
      { nombre: "Nayarit", abreviatura: "Nay.", IDH: [randomIDH(),randomIDH(),randomIDH(),randomIDH(),randomIDH()] },
      { nombre: "Nuevo León", abreviatura: "N.L.", IDH: [randomIDH(),randomIDH(),randomIDH(),randomIDH(),randomIDH()] },
      { nombre: "Oaxaca", abreviatura: "Oax.", IDH: [randomIDH(),randomIDH(),randomIDH(),randomIDH(),randomIDH()] },
      { nombre: "Puebla", abreviatura: "Pue.", IDH: [randomIDH(),randomIDH(),randomIDH(),randomIDH(),randomIDH()] },
      { nombre: "Querétaro", abreviatura: "Qro.", IDH: [randomIDH(),randomIDH(),randomIDH(),randomIDH(),randomIDH()] },
      { nombre: "Quintana Roo", abreviatura: "Q.R.", IDH: [randomIDH(),randomIDH(),randomIDH(),randomIDH(),randomIDH()] },
      { nombre: "San Luis Potosí", abreviatura: "S.L.P.", IDH: [randomIDH(),randomIDH(),randomIDH(),randomIDH(),randomIDH()] },
      { nombre: "Sinaloa", abreviatura: "Sin.", IDH: [randomIDH(),randomIDH(),randomIDH(),randomIDH(),randomIDH()] },
      { nombre: "Sonora", abreviatura: "Son.", IDH: [randomIDH(),randomIDH(),randomIDH(),randomIDH(),randomIDH()] },
      { nombre: "Tabasco", abreviatura: "Tab.", IDH: [randomIDH(),randomIDH(),randomIDH(),randomIDH(),randomIDH()] },
      { nombre: "Tamaulipas", abreviatura: "Tamps.", IDH: [randomIDH(),randomIDH(),randomIDH(),randomIDH(),randomIDH()] },
      { nombre: "Tlaxcala", abreviatura: "Tlax.", IDH: [randomIDH(),randomIDH(),randomIDH(),randomIDH(),randomIDH()] },
      { nombre: "Veracruz", abreviatura: "Ver.", IDH: [randomIDH(),randomIDH(),randomIDH(),randomIDH(),randomIDH()] },
      { nombre: "Yucatán", abreviatura: "Yuc.", IDH: [randomIDH(),randomIDH(),randomIDH(),randomIDH(),randomIDH()] },
      { nombre: "Zacatecas", abreviatura: "Zac.", IDH: [randomIDH(),randomIDH(),randomIDH(),randomIDH(),randomIDH()] },
    ]
  })
};
</script>

<style>
#app {
  font-family: "Open Sans", Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #282f36;
  margin-top: 30px;
}
</style>
