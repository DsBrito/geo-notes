- #aprendendo [[vue]] [[slot]] avançado para usar no [[geofrota]]
	- #ref https://vuejs.org/guide/components/slots.html
	- O slot simplesmente, como em `<slot/>`, é único por componente. Observe:
	  collapsed:: true
		- ```javascript
		  // FancyButton.vue
		  <template>
		    <button class="fancy-btn">
		    	<slot/> <!-- slot outlet -->
		  	</button>
		      <button class="fancy-btn">
		    	<slot/> <!-- slot outlet -->
		  	</button>
		  </template>
		  
		  <style>
		  .fancy-btn {
		    color: #fff;
		    background: linear-gradient(315deg, #42d392 25%, #647eff);
		    border: none;
		    padding: 5px 10px;
		    margin: 5px;
		    border-radius: 8px;
		    cursor: pointer;
		  }
		  </style>
		  
		  // App.vue
		  <script setup>
		  import FancyButton from './FancyButton.vue'
		  </script>
		  
		  <template>
		    <FancyButton>
		      Click me <!-- slot content -->
		   	</FancyButton>
		  </template>
		  ```
		- Isso resulta em dois botões identicos. Ambos os slots são ocupados. ![image.png](../assets/image_1732817498066_0.png)
	- For these cases, the `<slot>` element has a special attribute, `name`, which can be used to assign a unique ID to different slots so you can determine where content should be rendered:
	-