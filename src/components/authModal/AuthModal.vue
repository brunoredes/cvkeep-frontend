<template>
	<app-modal ref="authModal" class="app-auth-modal" :hasfooter="false" v-show="active" @close="close">
		<span class="close-it"
			@click="close"
			@keyup.enter="close"
			tabindex="0"
			role="button"
			:aria-label="$t('a11y.close')"
			>
			<i class="fa fa-times" aria-hidden="true"></i>
		</span>

		<div class="auth-modal-content">
			<swiper :options="swiperOptions" ref="sw">

				<!-- SIGNIN -->

				<swiper-slide>
					<div class="text" id="headerTitle">
						<h4>{{ $t('doLogin') }}</h4>
					</div>

					<div v-if="!withEmail" class="socials">
						<social-login @emailClick="withEmail = !withEmail"/>
						<p>{{ $t('stillNotRegistered') }} <strong><a href="/" @click.prevent="slideNext">{{ $t('registerYourself') }}</a></strong></p>
					</div>

					<div v-else>
						<sign-in-form
							@error="error"
							@go-back="hideForm"
							@forgot-pass="slideTo(2)"
						/>
					</div>
				</swiper-slide>

				<!-- REGISTER -->

				<swiper-slide>
					<div class="text">
						<h4>{{ $t('registerYourself') }}</h4>
					</div>

					<div v-if="!withEmail" class="socials">
						<social-login @emailClick="withEmail = !withEmail" :label="$t('registerUsing')"/>
						<p>{{ $t('alreadyRegistered') }} <strong><a href="/" @click.prevent="slidePrev">{{ $t('doLogin') }}</a></strong></p>
					</div>

					<div v-else>
						<register-form
							@error="error"
							@go-back="hideForm"
						/>
					</div>
				</swiper-slide>

				<!-- FORGOT PASSWORD -->

				<swiper-slide class="auth-register">
					<div class="text">
						<h4>{{ $t('forgotPassword') }}</h4>
						<p>{{ $t('forgotPasswordNoProb') }}</p>
					</div>

					<div>
						<forgot-pass-form
							@error="error"
							@go-to-login="slideTo(0)"
							@go-to-register="slideTo(1)"
						/>
					</div>
				</swiper-slide>
			</swiper>
		</div>

		<div class="terms">
			<p><small>{{ $t('termsAndConditionAgreement') }} <router-link to="/terms" :aria-label="$t('termsAndConditions')">{{ $t('termsAndConditions') }}</router-link></small></p>
		</div>

	</app-modal>
</template>

<script>

/* You can manage this modal from anywhere using
this.$AuthModal.show() or this.$AuthModal.close() */

import { swiper, swiperSlide } from 'vue-awesome-swiper'
import AuthModal from '@/plugins/AuthModal.js';
import AppModal from '@/components/lightbox/Lightbox.vue'
import SocialLogin from '@/components/authModal/SocialLogin.vue';
import SignInForm from './forms/SignInForm.vue';
import RegisterForm from './forms/RegisterForm.vue';
import ForgotPassForm from './forms/ForgotPassForm.vue';

export default {
	name: 'auth-modal',

	components: {
		AppModal,
		swiper,
		swiperSlide,
		SocialLogin,
		SignInForm,
		RegisterForm,
		ForgotPassForm,
	},

	data() {
		return {
			active: false,
			withEmail: false,
			signInText: '',
			signUpText: '',
			forgotPassText: '',
			swiperOptions: {
				allowTouchMove: false,
				keyboard: {
					enabled: false
				},
			},
		}
	},

	methods: {
		show(params) {
			!params && (params = {});

			this.withEmail = false;
			this.slideTo(params.index || 0);
			this.signInText = params.signInText ? params.signInText : '';
			this.signUpText = params.signUpText ? params.signUpText : '';

			if (this.$refs.sw) {
				const forms = this.$refs.sw.$el.querySelectorAll('form');

				forms.forEach(form => form.reset());
			}

			this.active = true;
		},

		close () {
			this.active = false;
		},

		slideNext () {
			this.withEmail = false;
			if(this.$refs.sw) this.$refs.sw.swiper.slideNext();
		},

		slidePrev () {
			this.withEmail = false;
			if(this.$refs.sw) this.$refs.sw.swiper.slidePrev();
		},

		slideTo (index) {
			this.withEmail = false;
			if(this.$refs.sw) this.$refs.sw.swiper.slideTo(index, 200);
		},

		error (error) {
			if (!error || !Array.from(error).length) {
				error = [ this.$i18n.t('errors.unexpectedError') ];
			}

			if (typeof error === 'string' || !error.forEach) {
				error = [ error ];
			}

			error.forEach(err => {
				this.$toasted.error(err);
			});
		},

		hideForm () {
			this.withEmail = ! this.withEmail;
		},
	},

	beforeMount() {
		AuthModal.EventBus.$on('show', (params) => {
			this.show(params);
		});

		AuthModal.EventBus.$on('close', (params) => {
			this.close(params);
		});
	},

	i18n: {
		messages: {
			'pt-br': {
				doLogin: 'Fazer Login',
				registerYourself: 'Registre-se',
				forgotPassword: 'Esqueci a senha',
				registerUsing: 'Registre-se utilizando',
				alreadyRegistered: 'Já possui cadastro?',
				termsAndConditions: 'Termos e Condições',
				stillNotRegistered: 'Ainda não é cadastrado?',
				forgotPasswordNoProb: 'Sem problemas! Por favor informe seu e-mail',
				termsAndConditionAgreement: 'Ao usar esta plataforma você automáticamente concorda com nossos',
			},

			'en': {
				doLogin: 'Login',
				registerYourself: 'Register',
				forgotPassword: 'Forgot password',
				registerUsing: 'Register using',
				alreadyRegistered: 'Already registered?',
				termsAndConditions: 'Terms and Conditions',
				stillNotRegistered: 'Not registered?',
				forgotPasswordNoProb: 'No problem! Please, inform your e-mail',
				termsAndConditionAgreement: 'By using this platform you automatically agree with our',
			},

			'fr': {
				doLogin: 'Se connecter',
				registerYourself: 'S\'enregistrer',
				forgotPassword: 'Mot de passe oublié',
				registerUsing: 'S\'enregistrer en utilisant',
				alreadyRegistered: 'Déjà inscrit ?',
				termsAndConditions: 'Conditions générales d\'utilisation',
				stillNotRegistered: 'Vous n\'êtes pas enregistré ?',
				forgotPasswordNoProb: 'Pas de problème ! Veuillez indiquer votre e-mail',
				termsAndConditionAgreement: 'En utilisant cette plateforme, vous acceptez automatiquement notre',
			}
		}
	}
}
</script>

<style lang="scss" scoped>
	.auth-modal-content {
		position: relative;
		padding: 48px var(--gutter);
		text-align: center;
		.text {
			> h4 {
				font-weight: 700;
				color: var(--primary-color);
			}
			> p {
				font-size: 20px;
				color: #555;
				font-weight: 600;
			}
		}
		.swiper-slide {
			visibility: hidden;
		}
		.swiper-slide.swiper-slide-active {
			visibility: visible;
		}
	}
	.terms {
		overflow: hidden;
		background-color: var(--primary-color);
		width: auto;
		padding: var(--gutter);
		position: relative;
		p {
			display: block;
			margin: 0;
			width: 100%;
			text-align: center;
			color: #ffffff;
		}
		a {
			color: #ffffff;
			text-decoration: underline;
		}
	}
</style>

<style lang="scss">
	.app-auth-modal {
		.close-it {
			position: absolute;
			right: var(--gutter);
			top: 7px;
			z-index: 1;
			cursor: pointer;
			opacity: .5;
			&:hover {
				opacity: 1;
			}
		}
		.cv-lightbox__content {
			height: auto;
		}
	}
</style>
