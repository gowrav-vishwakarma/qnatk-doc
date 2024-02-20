# useDataTable

A composable to handle data table operations.

```vue

<template>
  <div>
    <q-btn
      label="Download CSV"
      icon="cloud_download"
      @click="downloadData"
    />
    <q-table
      title="User List"
      :rows="data"
      :columns="columns"
      :pagination.sync="pagination"
      :loading="loading"
      row-key="id"
      @request="onRequest"
    >
    </q-table>
  </div>
</template>

<script setup lang="ts">
//  Other imports

import { useMyDatatable } from 'src/composables/use-my-datatable-wrapper';

const {
  data,
  pagination,
  loading,
  fetchData,
  onRequest,
  downloadData,
  callBacks,
  lacHookName
} = useMyDatatable('YourBackendModelName');


// lacHookName [optional]
// 1) To bypass default autoApi calling and reach to named hook 
// 2) with ?BeInformed will distinguish the request in network for easy debug and monitoring
lacHookName.value = '?BeInformed'; 

const userStore = useUserStore();


// Define default table pagination sort options, then follows table columns sorting
pagination.value.sortBy = 'lastActivityCreatedAt';
pagination.value.descending = true;


// Define table columns
const columns:QTableColumn[] = [
  { name: 'name', label: 'Name', field: 'name', align: 'left', sortable: true },
  { name: 'email', label: 'Email', field: 'email', align: 'left', sortable: true },
  // Add more columns as needed
  { name: 'actions', label: 'Actions', field: 'actions', align: 'right', sortable: false },
];

// Custom row iterator for CSV download to flatten nested objects if needed
callBacks.downloadRowIterator = (row) => ({
  Name: row.name,
  Email: row.email,
  // Transform or add additional fields as needed for the download
});

fetchOptions.value = {
  include: [
    {
      model: 'User',
      as: 'assignedTo',
      attributes: ['id', 'name', 'departmentId'],
    },
    {
      model: 'User',
      as: 'validator',
      attributes: ['id', 'name', 'departmentId'],
    },
    {
      model: 'User',
      as: 'createdBy',
      attributes: ['id', 'name', 'departmentId'],
    },
    {
      model: 'TaskUser',
      as: 'stakeholders',
      attributes: ['id', 'userId'],
    },
  ],
  where: {
    $or: {
      validatorId: userStore.user?.id,
      '$stakeholders.userId$': userStore.user?.id,
      status: ['assigned', 'validated'],
    },
    organizationId: userStore.user?.organizationId,
  },
  subQuery: false,
};

onMounted(() => {
  fetchData();
});

</script>

```
