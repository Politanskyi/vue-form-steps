<template>
	<div class="container mt-4">
		<div class="row">
			<div class="col-sm-6 mx-auto">
				<form @submit.prevent="registerUser" novalidate>
					<div v-show="step === 1" class="step">
						<div class="mb-3">
							<label for="name" class="form-label">Your name</label>
							<input v-model="formReg.name"
								   @blur="v$.formReg.name.$touch"
								   :class="status('name')"
								   type="text"
								   class="form-control" id="name">
							<div v-if="v$.formReg.name.required.$invalid"
								 class="invalid-feedback">
								{{ msgRequired }}
							</div>

							<div v-if="v$.formReg.name.alpha.$invalid"
								 class="invalid-feedback">
								{{ msgNotNumber }}
							</div>
						</div>

						<div class="mb-3">
							<label for="surname" class="form-label">Your last name</label>
							<input v-model="formReg.surname"
								   @blur="v$.formReg.surname.$touch"
								   :class="status('surname')"
								   type="text"
								   class="form-control"
								   id="surname">

							<div v-if="v$.formReg.surname.required.$invalid"
								 class="invalid-feedback">
								{{ msgRequired }}
							</div>

							<div v-if="v$.formReg.surname.alpha.$invalid"
								 class="invalid-feedback">
								{{ msgNotNumber }}
							</div>
						</div>

						<div class="mb-3">
							<label for="email" class="form-label">Your email</label>
							<input v-model="formReg.email"
								   @blur="v$.formReg.email.$touch"
								   :class="status('email')"
								   type="text"
								   class="form-control"
								   id="email">
							<div v-if="v$.formReg.email.required.$invalid"
								 class="invalid-feedback">
								{{ msgRequired }}
							</div>

							<div v-if="v$.formReg.email.email.$invalid"
								 class="invalid-feedback">
								Must be email address.
							</div>
						</div>

						<button @click="nextStep"
								:disabled="disabledButtonOne"
								type="button"
								class="btn btn-primary">
							Next step
						</button>
					</div>

					<transition name="slide-fade">
						<div v-show="step === 2" class="step">
							<div class="mb-3">
								<label for="newPassword" class="form-label">Password</label>
								<input v-model.trim="formReg.newPassword"
									   @blur="v$.formReg.newPassword.$touch"
									   :class="status('newPassword')"
									   ref="newPassword"
									   type="text"
									   class="form-control"
									   id="newPassword">

								<div v-if="v$.formReg.newPassword.required.$invalid"
									 class="invalid-feedback">
									{{ msgRequired }}
								</div>

								<div v-if="v$.formReg.newPassword.minLength.$invalid"
									 class="invalid-feedback">
									Min length 6 symbols.
								</div>
							</div>

							<div class="mb-3">
								<label for="repeatPassword" class="form-label">Repeat password</label>
								<input v-model.trim="formReg.repeatPassword"
									   @blur="v$.formReg.repeatPassword.$touch"
									   :class="status('repeatPassword')"
									   type="text"
									   class="form-control"
									   id="repeatPassword">

								<div v-if="v$.formReg.repeatPassword.required.$invalid"
									 class="invalid-feedback">
									Password not same.
								</div>
							</div>

							<button @click="prevStep"
									type="button"
									class="btn btn-light me-2">
								Prev step
							</button>

							<button @click="nextStep"
									:disabled="disabledButtonTwo"
									type="button"
									class="btn btn-primary">
								Next step
							</button>
						</div>
					</transition>

					<transition name="slide-fade">
						<div v-show="step === 3" class="step">
							<div class="mb-3">
								<label for="country" class="form-label">Country</label>
								<input v-model="formReg.country"
									   @blur="v$.formReg.country.$touch"
									   :class="status('country')"
									   type="text"
									   class="form-control"
									   id="country">

								<div v-if="v$.formReg.country.alpha.$invalid"
									 class="invalid-feedback">
									{{ msgNotNumber }}
								</div>
							</div>

							<div class="mb-3">
								<label for="city" class="form-label">City</label>
								<input v-model="formReg.city"
									   @blur="v$.formReg.city.$touch"
									   :class="status('city')"
									   type="text"
									   class="form-control"
									   id="city">

								<div v-if="v$.formReg.city.alpha.$invalid"
									 class="invalid-feedback">
									{{ msgNotNumber }}
								</div>
							</div>

							<button @click="prevStep"
									type="button"
									class="btn btn-light me-2">
								Prev step
							</button>
							<button @click="nextStep"
									:disabled="disabledRegister"
									type="submit"
									class="btn btn-primary">
								Submit
							</button>
						</div>
					</transition>
				</form>
			</div>
		</div>
	</div>
</template>

<script>
import useVuelidate from '@vuelidate/core';
import { required, email, helpers, minLength } from '@vuelidate/validators';

const alpha = helpers.regex(/^[a-zA-Zа-яА-ЯёЁ]*$/);

export default {
	setup() {
		return {
			v$: useVuelidate()
		}
	},

	data() {
		return {
			step: 1,
			msgNotNumber: 'Only symbols.',
			msgRequired: 'Field is required.',
			formReg: {
				name: '',
				surname: '',
				email: '',
				newPassword: '',
				repeatPassword: '',
				country: '',
				city: ''
			}
		}
	},

	computed: {
		disabledButtonOne() {
			return this.v$.formReg.name.$invalid || this.v$.formReg.surname.$invalid || this.v$.formReg.email.$invalid;
		},
		disabledButtonTwo() {
			let pass = this.formReg.newPassword;
			let rePass = this.formReg.repeatPassword;

			if (pass === rePass) {
				return this.v$.formReg.newPassword.$invalid || this.v$.formReg.repeatPassword.$invalid;
			} else {
				return 'disabled';
			}
		},
		disabledRegister() {
			return this.v$.formReg.country.$invalid || this.v$.formReg.city.$invalid;
		}
	},

	methods: {
		status(fieldName) {
			return {
				'is-invalid': this.v$.formReg[fieldName].$error
			};
		},
		registerUser() {
			console.log('registered');

			this.step = 1;

			for (let input in this.formReg) {
				this.formReg[input] = '';
			}

			this.v$.$reset();
		},
		prevStep() {
			if (this.step > 1) {
				this.step--;
			}
		},
		nextStep() {
			if (this.step < 3) {
				this.step++;
			}
		}
	},

	validations: {
		formReg: {
			name: {
				required,
				alpha
			},
			surname: {
				required,
				alpha
			},
			email: {
				required,
				email
			},
			newPassword: {
				required,
				minLength: minLength(6)
			},
			repeatPassword: { required },
			country: { alpha },
			city: { alpha }
		}
	}
}
</script>

<style>
.slide-fade-enter-active {
	transition: all 0.2s ease-out;
}

.slide-fade-enter-from {
	transform: translateX(10px);
	opacity: 0;
}
</style>
