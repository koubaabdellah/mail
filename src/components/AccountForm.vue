<template>
	<form id="account-form" @submit.prevent="onSubmit">
		<Tabs
			:options="{useUrlFragment: false, defaultTabHash: settingsPage ? 'manual' : 'auto'}"
			cache-lifetime="0"
			@changed="onModeChanged">
			<Tab id="auto" key="auto" :name="t('mail', 'Auto')">
				<label for="auto-name">{{ t('mail', 'Name') }}</label>
				<input
					id="auto-name"
					v-model="accountName"
					type="text"
					:placeholder="t('mail', 'Name')"
					:disabled="loading"
					autofocus>
				<label for="auto-address" class="account-form__label--required">{{ t('mail', 'Mail address') }}</label>
				<input
					id="auto-address"
					v-model.lazy="emailAddress"
					:disabled="loading"
					:placeholder="t('mail', 'name@example.org')"
					required
					type="email"
					@blur="isValidEmail(emailAddress)"
					@change="clearFeedback">
				<p v-if="!isValidEmail(emailAddress)" class="account-form--error">
					{{ t('mail', 'Please enter an email of the format name@example.com') }}
				</p>
				<label for="auto-password" class="account-form__label--required">{{ t('mail', 'Password') }}</label>
				<input
					id="auto-password"
					v-model="autoConfig.password"
					:disabled="loading"
					:placeholder="t('mail', 'Password')"
					required
					type="password"
					@change="clearFeedback">
			</Tab>
			<Tab id="manual" key="manual" :name="t('mail', 'Manual')">
				<label for="man-name">{{ t('mail', 'Name') }}</label>
				<input
					id="man-name"
					v-model="accountName"
					type="text"
					:placeholder="t('mail', 'Name')"
					:disabled="loading">
				<label for="man-address" class="account-form__label--required">{{ t('mail', 'Mail address') }}</label>
				<input
					id="man-address"
					v-model.lazy="emailAddress"
					type="email"
					:placeholder="t('mail', 'name@example.org')"
					:disabled="loading"
					required
					@blur="isValidEmail(emailAddress)"
					@change="clearFeedback">
				<p v-if="!isValidEmail(emailAddress)" class="account-form--error">
					{{ t('mail', 'Please enter an email of the format name@example.com') }}
				</p>

				<h3>{{ t('mail', 'IMAP Settings') }}</h3>
				<label for="man-imap-host" class="account-form__label--required">{{ t('mail', 'IMAP Host') }}</label>
				<input
					id="man-imap-host"
					v-model="manualConfig.imapHost"
					type="text"
					:placeholder="t('mail', 'IMAP Host')"
					:disabled="loading"
					required
					@change="clearFeedback">
				<h4 class="account-form__heading--required">
					{{ t('mail', 'IMAP Security') }}
				</h4>
				<div class="flex-row">
					<input
						id="man-imap-sec-none"
						v-model="manualConfig.imapSslMode"
						type="radio"
						name="man-imap-sec"
						:disabled="loading"
						value="none"
						@change="onImapSslModeChange">
					<label
						class="button"
						for="man-imap-sec-none"
						:class="{primary: manualConfig.imapSslMode === 'none'}">{{ t('mail', 'None') }}</label>
					<input
						id="man-imap-sec-ssl"
						v-model="manualConfig.imapSslMode"
						type="radio"
						name="man-imap-sec"
						:disabled="loading"
						value="ssl"
						@change="onImapSslModeChange">
					<label
						class="button"
						for="man-imap-sec-ssl"
						:class="{primary: manualConfig.imapSslMode === 'ssl'}">{{ t('mail', 'SSL/TLS') }}</label>
					<input
						id="man-imap-sec-tls"
						v-model="manualConfig.imapSslMode"
						type="radio"
						name="man-imap-sec"
						:disabled="loading"
						value="tls"
						@change="onImapSslModeChange">
					<label
						class="button"
						for="man-imap-sec-tls"
						:class="{primary: manualConfig.imapSslMode === 'tls'}">{{ t('mail', 'STARTTLS') }}</label>
				</div>
				<label for="man-imap-port" class="account-form__label--required">{{ t('mail', 'IMAP Port') }}</label>
				<input
					id="man-imap-port"
					v-model="manualConfig.imapPort"
					type="number"
					:placeholder="t('mail', 'IMAP Port')"
					:disabled="loading"
					required
					@change="clearFeedback">
				<label for="man-imap-user" class="account-form__label--required">{{ t('mail', 'IMAP User') }}</label>
				<input
					id="man-imap-user"
					v-model="manualConfig.imapUser"
					type="text"
					:placeholder="t('mail', 'IMAP User')"
					:disabled="loading"
					required
					@change="clearFeedback">
				<label for="man-imap-password" class="account-form__label--required">{{ t('mail', 'IMAP Password') }}</label>
				<input
					id="man-imap-password"
					v-model="manualConfig.imapPassword"
					type="password"
					:placeholder="t('mail', 'IMAP Password')"
					:disabled="loading"
					required
					@change="clearFeedback">

				<h3>{{ t('mail', 'SMTP Settings') }}</h3>
				<label for="man-smtp-host" class="account-form__label--required">{{ t('mail', 'SMTP Host') }}</label>
				<input
					id="man-smtp-host"
					ref="smtpHost"
					v-model="manualConfig.smtpHost"
					type="text"
					name="smtp-host"
					:placeholder="t('mail', 'SMTP Host')"
					:disabled="loading"
					required
					@change="clearFeedback">
				<h4 class="account-form__heading--required">
					{{ t('mail', 'SMTP Security') }}
				</h4>
				<div class="flex-row">
					<input
						id="man-smtp-sec-none"
						v-model="manualConfig.smtpSslMode"
						type="radio"
						name="man-smtp-sec"
						:disabled="loading"
						value="none"
						@change="onSmtpSslModeChange">
					<label
						class="button"
						for="man-smtp-sec-none"
						:class="{primary: manualConfig.smtpSslMode === 'none'}">{{ t('mail', 'None') }}</label>
					<input
						id="man-smtp-sec-ssl"
						v-model="manualConfig.smtpSslMode"
						type="radio"
						name="man-smtp-sec"
						:disabled="loading"
						value="ssl"
						@change="onSmtpSslModeChange">
					<label
						class="button"
						for="man-smtp-sec-ssl"
						:class="{primary: manualConfig.smtpSslMode === 'ssl'}">{{ t('mail', 'SSL/TLS') }}</label>
					<input
						id="man-smtp-sec-tls"
						v-model="manualConfig.smtpSslMode"
						type="radio"
						name="man-smtp-sec"
						:disabled="loading"
						value="tls"
						@change="onSmtpSslModeChange">
					<label
						class="button"
						for="man-smtp-sec-tls"
						:class="{primary: manualConfig.smtpSslMode === 'tls'}">{{ t('mail', 'STARTTLS') }}</label>
				</div>
				<label for="man-smtp-port" class="account-form__label--required">{{ t('mail', 'SMTP Port') }}</label>
				<input
					id="man-smtp-port"
					v-model="manualConfig.smtpPort"
					type="number"
					:placeholder="t('mail', 'SMTP Port')"
					:disabled="loading"
					required
					@change="clearFeedback">
				<label for="man-smtp-user" class="account-form__label--required">{{ t('mail', 'SMTP User') }}</label>
				<input
					id="man-smtp-user"
					v-model="manualConfig.smtpUser"
					type="text"
					:placeholder="t('mail', 'SMTP User')"
					:disabled="loading"
					required
					@change="clearFeedback">
				<label for="man-smtp-password" class="account-form__label--required">{{ t('mail', 'SMTP Password') }}</label>
				<input
					id="man-smtp-password"
					v-model="manualConfig.smtpPassword"
					type="password"
					:placeholder="t('mail', 'SMTP Password')"
					:disabled="loading"
					required
					@change="clearFeedback">
			</Tab>
		</Tabs>
		<div class="account-form__submit-buttons">
			<ButtonVue v-if="mode === 'auto'"
				class="account-form__submit-button"
				type="primary"
				native-type="submit"
				:disabled="isDisabledAuto || loading"
				@click.prevent="onSubmit">
				<template #icon>
					<IconLoading v-if="loading" :size="20" />
					<IconCheck v-else :size="20" />
				</template>
				{{ submitButtonText }}
			</ButtonVue>

			<ButtonVue v-else-if="mode === 'manual'"
				class="account-form__submit-button"
				type="primary"
				native-type="submit"
				:disabled="isDisabledManual || loading"
				@click.prevent="onSubmit">
				<template #icon>
					<IconLoading v-if="loading" :size="20" />
					<IconCheck v-else :size="20" />
				</template>
				{{ submitButtonText }}
			</ButtonVue>
		</div>
		<div v-if="feedback" class="account-form--feedback">
			{{ feedback }}
		</div>
	</form>
</template>

<script>
import { Tab, Tabs } from 'vue-tabs-component'
import { NcButton as ButtonVue, NcLoadingIcon as IconLoading } from '@nextcloud/vue'
import IconCheck from 'vue-material-design-icons/Check'
import { translate as t } from '@nextcloud/l10n'

import logger from '../logger'
import { testConnectivity, queryIspdb, queryMx } from '../service/AutoConfigService'

export default {
	name: 'AccountForm',
	components: {
		Tab,
		Tabs,
		ButtonVue,
		IconLoading,
		IconCheck,
	},
	props: {
		displayName: {
			type: String,
			default: '',
		},
		email: {
			type: String,
			default: '',
		},
		account: {
			type: Object,
			required: false,
			default: () => undefined,
		},
	},
	data() {
		const fromAccountOr = (prop, def) => {
			if (this.account !== undefined) {
				return this.account[prop]
			} else {
				return def
			}
		}

		return {
			loading: false,
			loadingMessage: undefined,
			mode: 'auto',
			accountName: this.displayName,
			emailAddress: this.email,
			autoConfig: {
				password: '',
			},
			manualConfig: {
				imapHost: fromAccountOr('imapHost', ''),
				imapPort: fromAccountOr('imapPort', 993),
				imapSslMode: fromAccountOr('imapSslMode', 'ssl'),
				imapUser: fromAccountOr('imapUser', ''),
				imapPassword: '',
				smtpHost: fromAccountOr('smtpHost', ''),
				smtpPort: fromAccountOr('smtpPort', 587),
				smtpSslMode: fromAccountOr('smtpSslMode', 'tls'),
				smtpUser: fromAccountOr('smtpUser', ''),
				smtpPassword: '',
			},
			feedback: null,
		}
	},
	computed: {
		settingsPage() {
			return this.account !== undefined
		},

		isDisabledAuto() {
			if (this.mode !== 'auto') {
				return this.loading
			}

			return !this.emailAddress || !this.isValidEmail(this.emailAddress)
				|| !this.autoConfig.password
		},

		isDisabledManual() {
			if (this.mode !== 'manual') {
				return this.loading
			}

			return !this.emailAddress || !this.isValidEmail(this.emailAddress)
				|| !this.manualConfig.imapHost || !this.manualConfig.imapPort
				|| !this.manualConfig.imapUser || !this.manualConfig.imapPassword
				|| !this.manualConfig.smtpHost || !this.manualConfig.smtpPort
				|| !this.manualConfig.smtpUser || !this.manualConfig.smtpPassword
		},

		submitButtonText() {
			if (this.loading) {
				return this.loadingMessage ?? t('mail', 'Connecting')
			}
			return this.account ? t('mail', 'Save') : t('mail', 'Connect')
		},
	},
	methods: {
		onModeChanged(e) {
			this.mode = e.tab.id

			if (this.mode === 'manual') {
				// IMAP
				if (this.manualConfig.imapUser === '') {
					this.manualConfig.imapUser = this.emailAddress
				}
				if (this.manualConfig.imapPassword === '') {
					this.manualConfig.imapPassword = this.autoConfig.password
				}

				// SMTP
				if (this.manualConfig.smtpUser === '') {
					this.manualConfig.smtpUser = this.emailAddress
				}
				if (this.manualConfig.smtpPassword === '') {
					this.manualConfig.smtpPassword = this.autoConfig.password
				}
			}
		},
		onImapSslModeChange() {
			this.clearFeedback()

			switch (this.manualConfig.imapSslMode) {
			case 'none':
			case 'tls':
				this.manualConfig.imapPort = 143
				break
			case 'ssl':
				this.manualConfig.imapPort = 993
				break
			}
		},
		onSmtpSslModeChange() {
			this.clearFeedback()

			switch (this.manualConfig.smtpSslMode) {
			case 'none':
			case 'tls':
				this.manualConfig.smtpPort = 587
				break
			case 'ssl':
				this.manualConfig.smtpPort = 465
				break
			}
		},
		clearFeedback() {
			this.feedback = null
		},
		applyAutoConfig(config) {
			if (!config) {
				return false
			}
			if (config?.imapConfig) {
				this.manualConfig.imapUser = config.imapConfig.username
				this.manualConfig.imapHost = config.imapConfig.host
				this.manualConfig.imapPort = config.imapConfig.port
				this.manualConfig.imapSslMode = config.imapConfig.security
				this.manualConfig.imapPassword = this.autoConfig.password
			}
			if (config?.smtpConfig) {
				this.manualConfig.smtpUser = config.smtpConfig.username
				this.manualConfig.smtpHost = config.smtpConfig.host
				this.manualConfig.smtpPort = config.smtpConfig.port
				this.manualConfig.smtpSslMode = config.smtpConfig.security
				this.manualConfig.smtpPassword = this.autoConfig.password
			}
			return true
		},
		async detectConfig() {
			this.loadingMessage = t('mail', 'Looking up configuration')
			const config = await queryIspdb(this.emailAddress)
			logger.debug('fetched auto config', { config })
			// Apply settings to manual mode before submitting so the user
			// can make modifications if the config fails
			if (this.applyAutoConfig(config)) {
				logger.debug('ISP DB config applied')
				return true
			} else {
				this.loadingMessage = t('mail', 'Checking mail host connectivity')
				const mxHosts = await queryMx(this.emailAddress)
				logger.debug('MX hosts fetched', { mxHosts })
				const imapAndSmtpHosts = mxHosts.flatMap(host => {
					return [993, 143, 465, 587].map(port => ({
						host,
						port,
					}))
				})
				const results = await Promise.all(imapAndSmtpHosts.map(async ({ host, port }) => {
					return {
						host,
						port,
						canConnect: await testConnectivity(host, port),
					}
				}))
				const firstImapHost = results.filter(({ canConnect, port }) => canConnect && port === 993)[0]
				const firstSmtpHost = results.filter(({ canConnect, port }) => canConnect && [465, 587].includes(port))[0]
				logger.debug('MX connectivity tested', { firstImapHost, firstSmtpHost })
				if (firstImapHost && firstSmtpHost) {
					this.applyAutoConfig({
						imapConfig: {
							username: this.emailAddress, // Assumption
							host: firstImapHost.host,
							port: firstImapHost.port,
							security: firstImapHost.port === 993 ? 'ssl' : 'tls',
						},
						smtpConfig: {
							username: this.emailAddress, // Assumption
							host: firstSmtpHost.host,
							port: firstSmtpHost.port,
							security: firstSmtpHost.port === 465 ? 'ssl' : 'tls',
						},
					})
					return true
				} else {
					this.feedback = t('mail', 'Configuration discovery failed. Please use the manual settings')
				}
				return false
			}
		},
		async onSubmit(event) {
			logger.debug('account form submitted', { event })
			if (this.isDisabledManual || this.isDisabledAuto) {
				logger.warn('submit is disabled')
				return
			}
			this.clearFeedback()
			this.loading = true
			try {
				if (this.mode === 'auto') {
					if (!await this.detectConfig()) {
						logger.warn('Auto mode failed')
						return
					}
				}
				this.loadingMessage = t('mail', 'Testing authentication')
				const data = {
					...this.manualConfig,
					accountName: this.accountName,
					emailAddress: this.emailAddress,
					imapHost: this.manualConfig.imapHost.trim(),
					smtpHost: this.manualConfig.smtpHost.trim(),
				}
				if (!this.account) {
					const account = await this.$store.dispatch('createAccount', data)
					this.feedback = t('mail', 'Account created')
					this.$emit('account-created', account)
				} else {
					const account = await this.$store.dispatch('updateAccount', {
						...data,
						accountId: this.account.id,
					})
					this.feedback = t('mail', 'Account updated')
					this.$emit('account-updated', account)
				}
				this.feedback = t('mail', 'Changes saved')
			} catch (error) {
				logger.error('could not save account details', { error })

				if (error.data?.error === 'CONNECTION_ERROR') {
					if (error.data.service === 'IMAP') {
						this.feedback = t('mail', 'IMAP server is not reachable')
					} else if (error.data.service === 'SMTP') {
						this.feedback = t('mail', 'SMTP server is not reachable')
					}
				} else if (error.data?.error === 'AUTHENTICATION') {
					if (error.data.service === 'IMAP') {
						this.feedback = t('mail', 'IMAP username or password is wrong')
					} else if (error.data.service === 'SMTP') {
						this.feedback = t('mail', 'SMTP username or password is wrong')
					}
				} else {
					if (error.data?.service === 'IMAP') {
						this.feedback = t('mail', 'IMAP connection failed')
					} else if (error.data?.service === 'SMTP') {
						this.feedback = t('mail', 'SMTP connection failed')
					} else {
						this.feedback = t('mail', 'There was an error while setting up your account')
					}
				}
			} finally {
				this.loading = false
				this.loadingMessage = undefined
			}
		},
		isValidEmail(email) {
			const regExpEmail = /^(([^<>()[\]\\.,;:\s@"]+(\.[^<>()[\]\\.,;:\s@"]+)*)|(".+"))@((\[[0-9]{1,3}\.[0-9]{1,3}\.[0-9]{1,3}\.[0-9]{1,3}\])|(([a-zA-Z\-0-9]+\.)+[a-zA-Z]{2,}))$/
			return regExpEmail.test(email)
		},
	},
}
</script>

<style lang="scss" scoped>
:deep(.tabs-component-tabs) {
	display: flex;
}

:deep(.tabs-component-tab) {
	flex-grow: 1;
	text-align: center;
	color: var(--color-text-lighter);
	margin-bottom: 10px;
}

:deep(.tabs-component-tab.is-active) {
	border-bottom: 1px solid black;
	font-weight: bold;
}

.tabs-component-panels {
	padding-top: 20px;
}

.tabs-component-panels label {
	text-align: left;
	width: 100%;
	display: inline-block;
}

.tabs-component-panels input,
.tabs-component-panels select {
	margin-bottom: 10px;
}
</style>

<style scoped>
h4 {
	text-align: left;
}

.flex-row {
	display: flex;
}

label.button {
	display: inline-block;
	text-align: center;
	flex-grow: 1;
}
label.primary {
	color: var(--color-main-background);
}
input.primary {
	color: var(--color-main-background);
}

input[type='radio'] {
	display: none;
}

input[type='radio'][disabled] + label {
	cursor: default;
	opacity: 0.5;
}
.account-form__label--required:after {
	content:" *";
}
.account-form__heading--required:after {
	content:" *";
}
.account-form__submit-buttons {
	display: flex;
	justify-content: center;
	margin-top: 5px;
}
.account-form__submit-button {
	display: flex;
	align-items: center;
}
.account-form--feedback {
	color: var(--color-text-maxcontrast);
	margin-top: 5px;
	text-align: center;
}
.account-form--error {
	text-align: left;
	font-size: 14px;
}
#account-form {
	z-index: 1001;
	width: 250px;
	top: 15%;
	padding-bottom: 50px;
	margin: 0 auto;
	padding-top: 30px;
}
#account-form input {
	width: 100%;
	box-sizing: border-box;
}
</style>
