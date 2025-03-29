<script>
  import Gun from 'gun';
  import { onMount } from 'svelte';

  const gun = Gun();
  let products = [];
  let name = '';
  let price = '';
  let description = '';

  const productRef = gun.get('products');

  function addProduct() {
    const id = Date.now().toString();
    const newProduct = { id, name, price, description };
    productRef.get(id).put(newProduct);

    name = '';
    price = '';
    description = '';
  }

  function loadProducts() {
    productRef.map().once((data, key) => {
      if (data && !products.find(p => p.id === data.id)) {
        products = [...products, data];
      }
    });
  }

  onMount(() => {
    loadProducts();
  });
</script>

<main class="p-4 max-w-xl mx-auto">
  <h1 class="text-2xl font-bold mb-4">Local Business Inventory</h1>

  <div class="mb-6">
    <input class="border p-2 w-full mb-2" placeholder="Product name" bind:value={name} />
    <input class="border p-2 w-full mb-2" placeholder="Price" bind:value={price} />
    <textarea class="border p-2 w-full mb-2" placeholder="Description" bind:value={description}></textarea>
    <button class="bg-blue-500 text-white px-4 py-2" on:click={addProduct}>Add Product</button>
  </div>

  <div>
    <h2 class="text-xl font-semibold mb-2">Product Listings</h2>
    {#each products as product}
      <div class="border rounded p-4 mb-2">
        <h3 class="font-bold text-lg">{product.name}</h3>
        <p>${product.price}</p>
        <p>{product.description}</p>
      </div>
    {/each}
  </div>
</main>

<style>
  body { font-family: sans-serif; }
</style>

