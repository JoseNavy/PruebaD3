<template>
  <div>

    <h2> <u> Indice de Desarrollo Humano </u></h2>

    <!-- Abrimos un container donde se encontraran los 3 dropdowns para seleccion -->
    <div class="container"> 

      <div class="row"> 
        
        <!-- Para organizar en columnas utilizamos Bootstrap -->
        <div class="col-sm">
          
          <p> Estado: </p>
          <div id="selectState">   </div>

        </div>

        <div class="col-sm">
          
          <p> Años: </p>
          <div id="SelectYear"> </div>

        </div>

        <div class="col-sm">
          
          <p> Orden de los datos: </p>
          <div id="SelectMe"> </div>

        </div>
    
      </div> 

    </div>

    <!-- Insertamos la grafica-->
    <div class="row justify-content-md-center" id="chartContainer"> </div>

    <!-- Acomodamos en renglones y columnas las estadisticas de maximo, minimo y promedio IDH del estado seleccionado -->
    <div class="container" id="stats"> 

      <div class="row justify-content-md-center"> 

        <div class="col-md-auto">

          <br />

          <p> <b>Estadisticas del estado de:</b> {{this.selectedState}} </p>
        
        </div>

      </div> 

      
      <div class="row justify-content-md-center"> 

        <div class="col-md-auto">

           <p> <b> Promedio IDH: </b> {{this.promedio}} </p>

        </div> 

      </div> 

      <div class="row justify-content-md-center"> 

        <div class="col-md-auto">

        <p> <b> IDH mas alto: </b> {{this.maximo}} </p>

        </div> 
      
      </div> 

      <div class="row justify-content-md-center"> 

        <div class="col-md-auto">

          <p> <b> IDH mas bajo: </b> {{this.minimo}} </p>

        </div> 
      
      </div> 
    
    </div>

  </div>
</template>

<script>

// Importamos d3 para graficar y crear los dropwdowns
import * as d3 from "d3";

export default {
  props: {
    xKey: String,
    yKey: String,
    xKeyMob: String,
    data: Array
  },
  mounted() {
    this.DropdownState();
    this.DropdownYear();
    this.DropdownOrder();
    this.Stats();
    this.ResizeListener()

    // Si el ancho de la ventana es menor a 480, renderiza la grafica en modo para movil, 
    // si no, renderiza la grafica para escritorio.
    if (this.anchoVentana <= 480) {
      this.AnimateLoadMobile();
    } else if (this.anchoVentana > 480) {
      this.AnimateLoad();
    }
  },

  data() {
      return {
        chart: null, // Donde se almacena la grafica
        selectedYear: 0, // Indice del año seleccionado por el usuario. Valor por default: 0 (el primer año en el rango)
        selectedState: "Aguascalientes", // Estado seleccionado por el usuario. Valor por default: Aguascalientes
        maximo: 0, // IDH maximo de un estado en el rango de años generado
        minimo: 0, // IDH minimo de un estado en el rango de años generado
        promedio: 0, // IDH promedio de un estado en el rango de años generado
        anchoVentana: window.innerWidth // Tamaño de la ventana
      }
  },

  methods: {

    // Este metodo revisa el estado del tamaño de la ventana (en pixeles).
    // Esto con el fin de saber que grafica renderizar en base al tamaño de la ventana. 
    ResizeListener() {
      
      window.addEventListener("resize", () => {

        this.anchoVentana = window.innerWidth
        const showChart = document.getElementById("chartContainer");
        
        // Si el ancho de la ventana es menor o igual a 480, removemos el componente renderizado
        // y se renderiza la grafica para movil. De lo contrario, se renderiza la grafica para escritorio.
        if (this.anchoVentana <= 480 && showChart.firstChild !== null) {

          showChart.firstChild.remove()
          this.AnimateLoadMobile();
          
        } else if(this.anchoVentana > 480){

          this.AnimateLoad();
        }
      })

    },

    // Este es el dropdown para seleccionar un estado, de forma que cambia las estadisticas mostradas
    // y se ilumina de color rojo el estado seleccionado 
    DropdownState() {
      
      // Obtenemos los estados del objeto "data"
      const states = this.data.map( d => d[this.xKey])

      // Utilizamos esta constante para hacer referencia a los datos y metodos fuera de la función que se
      // presenta mas adelante
      const ref = this

      // Creamos el dropdown, seleccionando la etiqueta correpondiente del template
      d3.select("#selectState")
        .append("span")
        .append("select")
        .attr("id", "stateSelection")
        .attr("name", "tasks")
        .selectAll("option")
        .data(states)
        .enter()
        .append("option")
        .attr("value", d => d)
        .text(d => d)

      // Añadimos una función de forma que cuando el valor del dropdown cambie, se colorea
      // el estado seleccionado
      d3.select("#stateSelection").on("change", function() {

        const selectedOption = d3.select(this).node().value;
        ref.selectedState = selectedOption;

        // Pintamos todos las barras del color general
        d3.selectAll("rect")
          .style("fill","#319bbe")

        // Pitanmos solo la barra que coincide con el estado seleccionado y la pintamos de rojo
        d3.selectAll("rect")
          .filter(function(d) { return d.nombre == selectedOption; }) 
          .style("fill", "red")

        // Al hacer esto, volvemos a renderizar el componente de estadisticas
        ref.Stats()

      })

    },

    // Este es el dropdown para seleccionar un año, de forma que cambia la grafica mostrada
    // mostrando los datos correpondientes al año seleccionado 
    DropdownYear(){
      
      // Primero creamos el rango de años que se podrán seleccionar, para esto, generamos un numero
      // aleatorio entre [1990, 2019], posteriormente generando un arreglo con los 4 años siguientes 
      // al año generado aleatoriamente. 
      const max = 2019
      const min = 1990
      const firstYear = Math.floor(Math.random() * (max - min + 1)) + min
      const years = [firstYear, firstYear+1, firstYear+2, firstYear+3, firstYear+4]

      // Utilizamos esta constante para hacer referencia a los datos y metodos fuera de la función que se
      // presenta mas adelante
      const ref = this

      // Creamos el dropdown, seleccionando la etiqueta correpondiente del template
      d3.select("#SelectYear")
        .append("span")
        .append("select")
        .attr("id", "yearSelection")
        .attr("name", "tasks")
        .selectAll("option")
        .data(years)
        .enter()
        .append("option")
        .attr("value", d => d)
        .text(d => d);

      // Al seleccionar el año, guardamos en el dato "selectedYear" el indice del año seleccionado, esto para posteriormente
      // ser usado a la hora de graficar. Como los datos del IDH vienen en un arreglo de [5], donde cada uno corresponde por orden
      //  al año cronologicamente, a la hora de graficar se utiliza este indice para saber que año debe de ser graficado. 
      d3.select("#yearSelection").on("change", function() {

        const selectedOption = d3.select(this).node().value;

        ref.selectedYear = years.indexOf(Number(selectedOption))
        
        // En esta condición, revisamos si debemos de renderizar la grafica para movil o para escritorio
        if (ref.anchoVentana <= 480) {

          ref.order()
          ref.AnimateLoadMobile();

        } else if(ref.anchoVentana > 480) {
          
          ref.order()
          ref.AnimateLoad()

        }

      
      })

    },

    // Este es el dropdown para seleccionar un orden, de forma que cambia la grafica mostrada
    // mostrando los datos ordenados segun lo indica el usuario
    DropdownOrder(){
      
      // Creamos el arrglo con las distintas opciones para ordenar
      const selectItems = ["Alphabetically", "Ascendingly", "Descendingly"];

      // Utilizamos esta constante para hacer referencia a los datos y metodos fuera de la función que se
      // presenta mas adelante
      let ref = this

      // Almacenamos el indice del año seleccionado en la siguiente variable para mas facilidad a la hora de llamarlo
      let year = this.selectedYear

      // Creamos el dropdown, seleccionando la etiqueta correpondiente del template
      d3.select("#SelectMe")
        .append("span")
        .append("select")
        .attr("id", "selection")
        .attr("name", "tasks")
        .selectAll("option")
        .data(selectItems)
        .enter()
        .append("option")
        .attr("value", d => d)
        .text(d => d);

        // Al detectar un cambio en el dropdown, se verifica con una serie de condiciones el orden seleccionado
        // dependiendo del orden selecionado, se organizan los datos a graficar, esto mediante el uso de las funciones
        // de ascending y descending de d3.
        d3.select("#selection").on("change", function() {

          const selectedOption = d3.select(this).node().value;
          if (selectedOption == "Ascendingly") {
            ref.data.sort((a,b) => {
             return d3.ascending(a[ref.yKey][year], b[ref.yKey][year])
            }) 
          } else if (selectedOption == "Descendingly") {
            ref.data.sort((a,b) => {
              return d3.descending(a[ref.yKey][year], b[ref.yKey][year])
            })
          } else if (selectedOption == "Alphabetically") {
            ref.data.sort((a,b) => {
              return d3.ascending(a[ref.xKey], b[ref.xKey])
            })
          }

          // En esta condición, revisamos si debemos de renderizar la grafica para movil o para escritorio
          if (ref.anchoVentana <= 480) {

            ref.order()
            ref.AnimateLoadMobile();

          } else if(ref.anchoVentana > 480) {
            
            ref.order()
            ref.AnimateLoad()

          }
        })
    },

    // Este metodo se utiliza para ordenar los datos sin renderizar de nuevo el dropdown de "Organización de Datos"
    order() {
      let ref = this
      let year = this.selectedYear

      // Seleccionamos la dropbox que tiene los cambios
      d3.select("#SelectMe")
        
        // Se realiza la misma acción que en la linea 290 de este componente
        d3.select("#selection").on("change", function() {

          const selectedOption = d3.select(this).node().value;
          if (selectedOption == "Ascendingly") {
            ref.data.sort((a,b) => {
             return d3.ascending(a[ref.yKey][year], b[ref.yKey][year])
            }) 
          } else if (selectedOption == "Descendingly") {
            ref.data.sort((a,b) => {
              return d3.descending(a[ref.yKey][year], b[ref.yKey][year])
            })
          } else if (selectedOption == "Alphabetically") {
            ref.data.sort((a,b) => {
              return d3.ascending(a[ref.xKey], b[ref.xKey])
            })
          }

          // En esta condición, revisamos si debemos de renderizar la grafica para movil o para escritorio
          if (ref.anchoVentana <= 480) {

            ref.order()
            ref.AnimateLoadMobile();

          } else {
            
            ref.order()
            ref.AnimateLoad()

          }
        })

    },

    // En este metodo es donde se carga la grafica y donde se insertan los datos para realizar la grafica. Este es el metodo
    // para graficar en el dado caso de que se visualice en un escritorio.
    AnimateLoad() {

      // Asignamos valores al svg y al tamaño de la grafica, ademas de darle margenes para que se acomode mejor. 
      const margin = 120;
      const svg_width = 1000;
      const svg_height = 600;
      const chart_width = 1000 - 2 * margin;
      const chart_height = 600 - 2 * margin;
      
      // Almacenamos el indice del año seleccionado en la siguiente variable para mas facilidad a la hora de llamarlo
      let year = this.selectedYear

      // Creamos una constante en donde se crea el div donde se insertará la grafica.
      const chartDIV = document.createElement("div")

      // Le damos las medidas al svg
      const svg = d3
          .select(chartDIV)
          .append("svg")
          .attr("width", svg_width)
          .attr("height", svg_height);

      // Almacenamos en this.chart el svg creado y lo transformamos con los margenes.
      this.chart = svg
          .append("g")
          .attr("transform", "rotate(90)")
          .attr("transform", `translate(${margin}, ${margin})`);

      // Creamos la escala para el eje y, donde colocamos su rango y su dominio
      // Las propiedades dataMin y dataMax obtienen el dato maximo y minimo de y,
      // esto con el fin de darle el dominio dependiendo de los datos generados.
      const yScale = d3
          .scaleLinear()
          .range([chart_height, 0])
          .domain([this.dataMin > 0 ? 0 : this.dataMin, this.dataMax]);

      // Con la llamada a la escala, creamos el eje para y
      this.chart
          .append("g")
          .call(d3.axisLeft(yScale))
          
      // Creamos la escala para el eje x, dnde colocamos su rango y su dominio
      // Su dominio está dado por la cantidad de estados que tenemos, en este caso, 32
      const xScale = d3
          .scaleBand()
          .range([0, chart_width])
          .domain(this.data.map(d => {
              return d[this.xKey];
            })
          )
          .padding(0.2);

      // Con la llamada a la escala, creamos el eje para x.
      // Con ayuda de selectAll, rotamos el texto de las etiquetas del eje
      this.chart
          .append("g")
          .attr("transform", `translate(0, ${chart_height})`)
          .call(d3.axisBottom(xScale))
          .selectAll("text")
          .attr("transform","rotate(90)")
          .attr("dy", "-0.2cm")
          .attr("dx", "1.2cm")

      // Empezamos a graficar las barras de la grafica. Para esto seleccionamos la data con .data()
      const barGroups = this.chart
          .selectAll("rect")
          .data(this.data)
          .enter();

      // Aqui añadimos una barra para cada uno de los estados, utilizando la funcion .attr()
      barGroups
          .append("rect")
          .attr("class", "bar")
          .attr("x", g => xScale(g[this.xKey]) + 2)
          .attr("y", g => yScale(g[this.yKey][year]))
          .attr("height", g => chart_height - yScale(g[this.yKey][year])) 
          .attr("width", xScale.bandwidth() - 2)

      // Los siguientes tres bloques de codigo, son para añadir texto a la grafica, para identificar 
      // que grafica cada eje. 
      svg
          .append('text')
          .attr('class', 'label')
          .attr('x', -(chart_height / 2) - margin)
          .attr('y', margin / 1.3)
          .attr('transform', 'rotate(-90)')
          .attr('text-anchor', 'middle')
          .text('IDH')
          

      svg
          .append('text')
          .attr('class', 'label')
          .attr('x', chart_width / 2 + margin)
          .attr('y', chart_height + margin * 1.9)
          .attr('text-anchor', 'middle')
          .text('Estado')

      svg
          .append('text')
          .attr('class', 'title')
          .attr('x', chart_width / 2 + margin)
          .attr('y', 40)
          .attr('text-anchor', 'middle')
          .text('IDH por Estado')

      // Con esta condición, el codigo dibuha de nuevo la grafica basado en la elección del dropdown del usuario. 
      const showChart = document.getElementById("chartContainer");

      while (showChart.firstChild) {
        showChart.firstChild.remove()
      }

      showChart.appendChild(chartDIV)


    },
       
    // En este metodo es donde se carga la grafica y donde se insertan los datos para realizar la grafica. Este es el metodo
    // para graficar en el dado caso de que se visualice en un movil.
    AnimateLoadMobile(){
      
      // Asignamos valores al svg y al tamaño de la grafica, ademas de darle margenes para que se acomode mejor. 
      const margin = 80;
      const svg_width = 600;
      const svg_height = 1000;
      const chart_width = 650 - 2 * 160;
      const chart_height = 1200 - 2 * 160;
      
      // Almacenamos el indice del año seleccionado en la siguiente variable para mas facilidad a la hora de llamarlo
      let year = this.selectedYear

      // Creamos una constante en donde se crea el div donde se insertará la grafica.
      const chartDIV = document.createElement("div")

      // Le damos las medidas al svg
      const svg = d3
          .select(chartDIV)
          .append("svg")
          .attr("width", svg_width)
          .attr("height", svg_height);

      // Almacenamos en this.chart el svg creado y lo transformamos con los margenes.
      this.chart = svg
          .append("g")
          .attr("transform", "rotate(90)")
          .attr("transform", `translate(${margin}, ${margin})`);

        // Creamos la escala para el eje x, donde colocamos su rango y su dominio
        // Las propiedades dataMin y dataMax obtienen el dato maximo y minimo de y,
        // esto con el fin de darle el dominio dependiendo de los datos generados.
      const xScale = d3
          .scaleLinear()
          .range([0, chart_width])
          .domain([this.dataMin > 0 ? 0 : this.dataMin, this.dataMax]);

      // Con la llamada a la escala, creamos el eje para x
      this.chart
          .append("g")
          .attr("transform", `translate(0, ${chart_height})`)
          .call(d3.axisBottom(xScale))

        // Creamos la escala para el eje y, dnde colocamos su rango y su dominio
        // Su dominio está dado por la cantidad de estados que tenemos, en este caso, 32. 
        // Es importante notar que aqui se utiliza xKeyMob, que es la llave que nos permite usar
        // las abreviaciones de los estados.
      const yScale = d3
          .scaleBand()
          .range([0, chart_height])
          .domain(this.data.map(d => {
              return d[this.xKeyMob];
            })
          )

      // Con la llamada a la escala, creamos el eje para x.
      this.chart
          .append("g")
          .call(d3.axisLeft(yScale))
          

      // Empezamos a graficar las barras de la grafica. Para esto seleccionamos la data con .data()
      const barGroups = this.chart
          .selectAll("rect")
          .data(this.data)
          .enter();

      // Aqui añadimos una barra para cada uno de los estados, utilizando la funcion .attr()
      barGroups
          .append("rect")
          .attr("class", "bar")
          .attr("x", xScale(0))
          .attr("y", g => yScale(g[this.xKeyMob]))
          .attr("width", g => xScale(g[this.yKey][year]))
          .attr("height", yScale.bandwidth() - 8) 

      // Los siguientes tres bloques de codigo, son para añadir texto a la grafica, para identificar 
      // que grafica cada eje. 
      svg
          .append('text')
          .attr('class', 'label')
          .attr('x', -(chart_height / 2) - margin)
          .attr('y', margin / 2)
          .attr('transform', 'rotate(-90)')
          .attr('text-anchor', 'middle')
          .text('Estado')
          

      svg
          .append('text')
          .attr('class', 'label')
          .attr('x', chart_width / 2 + margin)
          .attr('y', chart_height + 1.5 * margin)
          .attr('text-anchor', 'middle')
          .text('IDH')

      svg
          .append('text')
          .attr('class', 'title')
          .attr('x', chart_width / 2 + margin)
          .attr('y', 40)
          .attr('text-anchor', 'middle')
          .text('IDH por Estado')


      // Con esta condición, el codigo dibuha de nuevo la grafica basado en la elección del dropdown del usuario. 
      const showChart = document.getElementById("chartContainer");

      while (showChart.firstChild) {
        showChart.firstChild.remove()
      }

      showChart.appendChild(chartDIV)

    },

    // Con este metodo, obtemos el IDH maximo, minimo y promedio del estado seleccionado en el rango de años generados.
    Stats() {

      let estadoObj = this.data.filter(b => {
        return b[this.xKey] === this.selectedState 
      })

      let listaIDH = estadoObj[0][this.yKey]

      // Almacenamos los datos en los datos del componente para ser renderizados en el template.
      this.maximo = d3.max(listaIDH)
      this.minimo = d3.min(listaIDH)
      this.promedio = d3.mean(listaIDH)

    }
  },

  computed: {
    
    // Obtiene el maximo IDH para el rango de años generados
    dataMax() {
      return d3.max(this.data, d => {
        return d[this.yKey][this.selectedYear];
      });
    },

    // Obtiene el minimo IDH para el rango de años generados
    dataMin() {
      return d3.min(this.data, d => {
        return d[this.yKey][this.selectedYear];
      });
    },

  }
};
</script>

<style>
.bar {
  fill: #319bbe;
}

.svg-container {
  display: inline-block;
  position: relative;
  width: 100%;
  padding-bottom: 1%;
  vertical-align: top;
  overflow: hidden;
}
</style>
