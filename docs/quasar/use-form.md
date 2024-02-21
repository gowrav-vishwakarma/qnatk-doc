# useForm

A composable to handle form operations.

> useForm<ResponseFormat extends Record<string, any>>(api: AxiosInstance, initialUrl: string, defaultValues: Record<string, any>, METHOD: 'post' | 'get' = 'post')

Returns

> * values: `Ref<Record<string, any>>` - The form values.
> * errors: `Ref<Record<string, string[]>>` - The form errors.
> * isLoading: `Ref<boolean>` - The loading state of the form.
> * validateAndSubmit: (resetForm = true) => `Promise<void>` - A function to validate and submit the form.
> * callbacks: FormCallBacks - A set of callback functions to handle form operations.
> * * onSuccess: (response: ResponseFormat) => `void` - A function to handle the form submission success.
> * * onError: (error: AxiosError) => `void` - A function to handle the form submission error.
> * * beforeSubmit: () => `void` - A function to be called before the form submission.
> * updateUrl: (url: string) => `void` - A function to update the form url.
> * responseData: `Ref<Response>` - The response data from the form submission.
> * resetForm: () => `void` - A function to reset the form values and errors.
> * validateResponse: (data, DTOClass) => `Promise<ResponseFormat>` - A function to validate the response data.

While useForm can be used to submit any form with values there are a few special urls that uses the autoApi system to get data like

* {baseUrl}/{BaseModel}/list
* {baseUrl}/{BaseModel}/list-and-count
* {baseUrl}/{BaseModel}/create
* {baseUrl}/{BaseModel}/create-with-files
* {baseUrl}/{BaseModel}/actionExecute/{actionName}/[skipModelLoad=false]
* {baseUrl}/{BaseModel}/update/{id}/[primaryField=id]
* {baseUrl}/{BaseModel}/delete/{id}/[primaryField=id]

to utilize the autoApi system the defaultValues should be treated like fetchOptions and the url should be one of the above mentioned urls.

ie
```ts
// remmber we are not passing api and baseUrl as we are using our own wrapper function here
const { values, validateAndSubmit, callbacks, errors } = useMMForm(
  '/Model/list',
  {
    where: {
      name: {
        $like: 'John',
      },
    },
  } as ModelOptions
);
```

```vue
<template>
  <q-page class="row items-center justify-center">
    <q-form @submit.prevent="validateAndSubmit">
      <q-card class="q-ma-md" style="max-width: 400px">
        <q-card-section>
          <div class="text-h6">Register</div>
          <q-input
            v-model="values.email"
            label="Email"
            :error="!!errors.email"
            :error-message="errors.email ? errors.email.join('; ') : ''"
          />
          <q-input
            v-model="values.password"
            label="Password"
            type="password"
            :rules="[
              (val) =>
                val.length > 5 || 'Password must be at least 6 characters',
            ]"
          />
          <q-input
            v-model="values.confirmPassword"
            label="Confirm Password"
            type="password"
            :rules="[
              (val) =>
                (val === values.password && !!val) || 'Passwords do not match',
            ]"
          />
        </q-card-section>
        <q-card-actions align="right">
          <q-btn label="Login" color="secondary" @click="goToLogin" flat />
          <q-btn label="Register" color="primary" type="submit" />
        </q-card-actions>
      </q-card>
    </q-form>
  </q-page>
</template>

<script setup lang="ts">
// Assuming you have a similar setup for registration
import { useMMForm } from 'src/composibles/use-mm-form';
import { useRouter } from 'vue-router';

const router = useRouter();

const { values, validateAndSubmit, callbacks, errors } = useMMForm(
  '/auth/register',
  {
    email: '',
    password: '',
  }
);

callbacks.onSuccess = () => {
  // Handle successful registration
  console.log('Registration success');
  router.push('/login');
};

const goToLogin = () => {
  router.push('/login'); // Navigate to the registration page
};
</script>
```

