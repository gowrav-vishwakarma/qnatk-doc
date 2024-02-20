## Qnatk-Q QuickStart

Qnatk is just a set of composables and only a few components. To make most of it you can opt in for best practices for your project as follows:

Qnatk consists of following composables:

- `useDataTable`: A composable to handle data table operations 
- `useForm`: A composable to handle form operations

Before using the composables it is always advisable to create a wrapper around the composables with your own api (axios instance).

create a file under composable in src named anything like this `use-MyForm.js` and add following code:    
```javascript
import { useForm } from 'src/qnatk/composibles/use-form';
import { api } from 'boot/axios';

// eslint-disable-next-line @typescript-eslint/no-explicit-any
export function useMMForm(
  initialUrl: string,
  // eslint-disable-next-line @typescript-eslint/no-explicit-any
  defaultValues: Record<string, any>,
  METHOD: 'post' | 'get' = 'post'
) {
  return useForm(api, initialUrl, defaultValues, METHOD);
}
```

In the same way you can create a wrapper for `useDataTable` composable.

```javascript
import { useDatatable } from 'src/qnatk/composibles/use-datatable';
import { TransformedSortOption } from 'src/qnatk/QnatkListDTO';
import { api } from 'boot/axios';

// eslint-disable-next-line @typescript-eslint/no-explicit-any
export function useMMDataTable<T = any>(
  baseModel: string,
  baseUrl = 'qnatk',
  transformSortBy: (sortBy: string) => string | TransformedSortOption = (
    sortBy
  ) => sortBy
) {
  return useDatatable<T>(api, baseModel, baseUrl, transformSortBy);
}
```

> This saves you to import api in every component and you can work with multiple api's in your project.

And following components:

- `AutoComplete`: A component to handle auto complete operations
- `SingleRecordActions`: A component to handle single record actions
- `NoRecordActions`: A component to handle no record actions
- `MultiRecordActions`: A component to handle multi record actions
- `SimpleForm`: A component to handle simple form operations