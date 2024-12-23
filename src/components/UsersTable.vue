<template>
  <!-- Sort and search elements -->
  <div class="filter-elements">
    <!-- Dropdown element -->
    <div class="dropdown-elements">
      <label for="sortColumn">Sort by: </label>
      <select v-model="sortColumn" id="sortColumn">
        <option v-for="(column, index) in filteredColumns" :key="index" :value="column">
          {{ column ? column.charAt(0).toUpperCase() + column.slice(1) : '' }}
        </option>
      </select>
      <button @click="toggleSortOrder" :disabled="!sortColumn">
        {{ sortOrder === 'asc' ? 'Ascending' : 'Descending' }}
      </button>
      <button @click="removeSortOrder" class="trash-icon"><i class="pi pi-trash"></i></button>
    </div>

    <!-- Search element -->
    <div>
      <label for="searchColumn">Search by: </label>
      <input type="search" v-model="searchColumn" id="searchColumn" />
    </div>
  </div>

  <!-- Users list table -->
  <div>
    <table>
      <thead>
        <tr>
          <th v-for="(column, index) in columns" :key="index">
            {{ column }}
          </th>
        </tr>
      </thead>
      <tbody>
        <tr v-for="(item, rowIndex) in filteredUsersList" :key="rowIndex">
          <td v-for="(column, colIndex) in columns" :key="colIndex">
            {{ item[column] }}
          </td>
        </tr>
      </tbody>
    </table>
  </div>
</template>

<script>
import { ref, computed } from 'vue'
import data from '../data/usersList.json'

export default {
  setup() {
    const usersList = ref(data)
    const sortColumn = ref('')
    const sortOrder = ref('asc')
    const searchColumn = ref('')

    // Detect columns dynamically
    const columns = computed(() => {
      return usersList.value.length > 0 ? Object.keys(usersList.value[0]) : []
    })

    // Filter out 'id' from the columns list for the dropdown
    const filteredColumns = computed(() => {
      return columns.value.filter((column) => column !== 'id')
    })

    // Computed property to sort and search elements in table
    const filteredUsersList = computed(() => {
      let filteredList = [...usersList.value]

      // Search filter by name, age and profile
      if (searchColumn.value) {
        filteredList = filteredList.filter(
          (item) =>
            item.name.toLowerCase().includes(searchColumn.value.toLowerCase()) ||
            String(item.age).toLowerCase().includes(searchColumn.value.toLowerCase()) ||
            item.profile.toLowerCase().includes(searchColumn.value.toLowerCase()),
        )
      }

      // Sorting elements
      if (sortColumn.value) {
        filteredList.sort((a, b) => {
          const valueA = a[sortColumn.value]
          const valueB = b[sortColumn.value]
          if (valueA < valueB) return sortOrder.value === 'asc' ? -1 : 1
          if (valueA > valueB) return sortOrder.value === 'asc' ? 1 : -1
          return 0
        })
      }

      return filteredList
    })

    // Toggle between ascending and descending order
    const toggleSortOrder = () => {
      sortOrder.value = sortOrder.value === 'asc' ? 'desc' : 'asc'
    }

    // Remove sort order
    const removeSortOrder = () => {
      sortOrder.value = 'asc'
      sortColumn.value = ''
    }

    return {
      usersList,
      columns,
      sortColumn,
      sortOrder,
      filteredUsersList,
      toggleSortOrder,
      filteredColumns,
      searchColumn,
      removeSortOrder,
    }
  },
}
</script>

<style scoped>
table {
  width: 50%;
  border-collapse: collapse;
  margin: 1rem auto;
}
th,
td {
  border: 1px solid #ddd;
  padding: 10px;
  text-align: center;
}
th {
  background-color: rgb(70, 70, 70);
  font-weight: bold;
  color: #fff;
}

.filter-elements {
  display: flex;
  flex-direction: column;
  align-items: center;
  gap: 1rem;
  padding: 1rem;
  background-color: #5c5c5c;
  border-radius: 8px;
  box-shadow: 0 2px 5px rgba(0, 0, 0, 0.1);
}

.dropdown-elements {
  display: flex;
  align-items: center;
  justify-content: center;
  gap: 0.5rem;
}

.trash-icon {
  background-color: #ff0000;
}

.trash-icon:hover:not(:disabled) {
  background-color: #e26363;
}
</style>
