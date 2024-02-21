# useForm

A composable to handle form operations.

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

