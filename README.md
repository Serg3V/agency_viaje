<!DOCTYPE html>
<html lang="en">
<head>
 <meta charset="UTF-8">
 <meta name="viewport" content="width=device-width, initial-scale=1.0">
 <link rel="stylesheet" href="styles.css">
 <title>Agencia de Viajes</title>
 <style>
    .search-container {
 text-align: center;
 margin-top: 20px;
}
#results-container {
 margin-top: 20px;
}
.result {
 border: 1px solid #ccc;
 padding: 10px;
 margin: 10px 0;
}
.offer {
  border: 2px solid #f39c12;
  padding: 10px;
  margin: 20px 0;
  background-color: #fdf2e3;
}
 </style>
</head>
<body>
 <div class="search-container">
   <input type="text" id="destination" placeholder="Destino" oninput="debounceSearch()">
   <input type="date" id="travel-date" onchange="search()">
   <button onclick="search()">Buscar</button>
 </div>
 <div id="results-container">
 </div>
 <script src="/socket.io/socket.io.js"></script>
 <script>
    
   let debounceTimeout;

   function debounceSearch() {
     clearTimeout(debounceTimeout);
     debounceTimeout = setTimeout(() => {
       search();
     }, 300);
   }
   
   async function search() {
     const destination = document.getElementById('destination').value;
     const travelDate = document.getElementById('travel-date').value;
     
     const response = await fetch(`/api/search?destination=${destination}&travelDate=${travelDate}`);
     const results = await response.json();
     

     const resultsContainer = document.getElementById('results-container');
     resultsContainer.innerHTML = '';
     results.forEach(result => {
       const resultDiv = document.createElement('div');
       resultDiv.className = 'result';
       resultDiv.innerHTML = `
         <h3>${result.title}</h3>
         <p>${result.description}</p>
         <p>Precio: ${result.price}</p>
       `;
       resultsContainer.appendChild(resultDiv);
     });
   }

   const socket = io();

   socket.on('specialOffer', (offer) => {
     const offerDiv = document.createElement('div');
     offerDiv.className = 'offer';
     offerDiv.innerHTML = `
       <h3>${offer.title}</h3>
       <p>${offer.description}</p>
       <p>Precio: ${offer.price}</p>
     `;
     document.body.appendChild(offerDiv);
   });
 </script>
</body>
</html>
