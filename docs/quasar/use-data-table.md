# useDataTable

A composable to handle data table operations.

> useDatatable`<T>`(
  api: AxiosInstance,
  baseModel: string,
  baseUrl = 'qnatk',
  transformSortBy: (sortBy: string) => string | TransformedSortOption = (
    sortBy
  ) => sortBy
) 
> 

Returns
> * data: `Ref<T[]>` - The data to be displayed in the table.
> * pagination: `Ref<QPagination>` - The pagination options.
> * loading: `Ref<boolean>` - The loading state of the table.
> * fetchData: () => `Promise<void>` - A function to fetch the data.
> * onRequest: (request: QTableRequest) => `void` - A function to handle the table request.
> * downloadData: () => `Promise<void>` - A function to download the data as a CSV file.
> * callBacks: DataTableCallBacks - A set of callback functions to handle table operations.
> * * downloadRowIterator: (row: T) => `Record<string, any>` - A function to transform the row data for the CSV download.
> * * rowIterator: (row: T) => `Record<string, any>` - A function to transform the row data for the table.
> * * aclCan: (actionName: string, baseModel: string): `boolean` => true - A function to check if the user has permission to perform an action.
> * fetchOptions: `Ref<ModelOptions>` - The fetch options to be used when fetching the data.
> * lacHookName: `Ref<string>` - The name of the hook to be used when fetching the data.
> * error: `Ref<Error | null>` - The error state of the table.

### fetchOptions 
The fetch options to be used when fetching the data. it mimics sequelize model options with additional properties like include, where, subQuery, etc.
following operators are supported in where clause:

* $or
* $and
* $eq
* $ne
* $gte
* $gt
* $lte
* $lt
* $not
* $in
* $notIn
* $like
* $notLike
* $iLike
* $notILike
* $regexp
* $notRegexp
* $iRegexp
* $notIRegexp
* $between
* $notBetween
* $fullText

To check null values you can use following (Case Sensitive) values:

'$null$' and '$notNull$' as follows

```ts
fetchOptions.value = {
  include: [
    {
      model: 'User',
      as: 'createdBy',
      attributes: ['id', 'name'],
    },
  ],

  where: {
    $or: {
      taskId: props.taskId,
      parentActivityId: props.taskId,
    },
    parentActivityId: '$null$',
  },
  order: ['createdAt', 'desc'],
};
```

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
