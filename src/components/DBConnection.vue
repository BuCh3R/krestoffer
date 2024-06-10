<script>
import { ref } from 'vue'

export function useDatabase() {

    const isLoading = ref(true);
    const items = ref([]);
    const search = ref("");
    let results = ref([]);

// get api with included search attribute
  const getItems = () => {
    fetch(`https://krestoffer-254e8-default-rtdb.europe-west1.firebasedatabase.app/items.json`, {
      method: 'GET'
    })
    .then((rawData) => {
      return rawData.json();
    })
    .then((data) => {
      items.value = data.items
      results = items.value.filter(obj => {
        return obj.name.toLowerCase().includes(search.value.toLowerCase());
      })
      items.value = results;
    })
    .finally(() => {
      isLoading.value = false;
    });
  }

  return {
    isLoading,
    items,
    search,
    results,
    getItems
  }
}
</script>
