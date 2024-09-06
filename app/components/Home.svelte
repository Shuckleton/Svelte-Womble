<page>
    <actionBar title="SVELTE-NATIVE" />
    <gridLayout columns="*, *" rows="*, *, *, auto">
        <label class="info" col="0" row="0" colSpan="2">
            <formattedString>
                <span class="fas" text="&#xf135;" />
                <span text=" {authStatus}" />
            </formattedString>
        </label>
        <button class="biometric-button" on:tap={loginWithBiometrics} col="0" row="1" colSpan="2">
            <formattedString>
                <span class="fas" text="&#xf2c6;" /> <!-- Fingerprint icon -->
                <span text=" Login with Biometrics" />
            </formattedString>
        </button>
        {#if authStatus === 'Login successful'}
            <label class="success-message" col="0" row="2" colSpan="2">
                <formattedString>
                    <span class="fas" text="&#xf00c;" /> <!-- Checkmark icon -->
                    <span text=" Login successful!" />
                </formattedString>
            </label>
        {/if}
    </gridLayout>
</page>

<script lang="ts">
    import { BiometricAuth, ERROR_CODES } from '@nativescript/biometrics';

    let authStatus = 'Not started';
    const biometricAuth = new BiometricAuth();

    async function loginWithBiometrics() {
        try {
            const result = await biometricAuth.available();
            if (result.any && result.biometrics) {
                const authResult = await biometricAuth.verifyBiometric({
                    title: 'Login',
                    message: 'Authenticate to log in',
                    fallbackMessage: 'Enter your PIN',
                    pinFallback: true,
                });
                if (authResult.code === ERROR_CODES.SUCCESS) {
                    authStatus = 'Login successful';
                    console.log('Biometric authentication successful');
                } else {
                    authStatus = `Login failed: ${authResult.code}`;
                    console.log(`Biometric authentication failed: ${JSON.stringify(authResult)}`);
                }
            } else {
                authStatus = 'Biometric authentication not available or not supported';
            }
        } catch (error) {
            authStatus = 'Error checking biometrics';
            console.error('Error checking biometrics:', error);
        }
    }
</script>

<style>
    .info {
        font-size: 20;
        horizontal-align: center;
        vertical-align: center;
        margin: 10;
        text-align: center;
    }

    .biometric-button {
        font-size: 18;
        margin: 10;
        background-color: #3A53FF;
        color: white;
        border-radius: 5;
        padding: 10;
    }

    .success-message {
        font-size: 18;
        color: green;
        text-align: center;
        margin: 10;
    }

    .info .fas {
        color: #3A53FF;
    }
</style>
