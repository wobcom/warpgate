<script lang="ts">
import { Input } from '@sveltestrap/sveltestrap'
import { CredentialKind, type User, type UserRequireCredentialsPolicy } from './lib/api'

interface Props {
    user: User;
    value: UserRequireCredentialsPolicy;
    possibleCredentials: Set<CredentialKind>;
    protocolId: 'http' | 'ssh' | 'mysql' | 'postgres';
}

let {
    user,
    value = $bindable(),
    possibleCredentials,
    protocolId,
}: Props = $props()

const labels = {
    Password: 'Password',
    PublicKey: 'Key',
    Totp: 'OTP',
    Sso: 'SSO',
    WebUserApproval: 'In-browser auth',
}

let isAny = $state(false)
const validCredentials = $derived.by(() => {
    let vc = new Set<CredentialKind>()
    vc = new Set(user.credentials.map(x => x.kind as CredentialKind))
    vc.add(CredentialKind.WebUserApproval)
    return vc
})

$effect(() => {
    isAny = !value[protocolId]
})

function updateAny () {
    if (isAny) {
        value[protocolId] = undefined
    } else {
        value[protocolId] = []
        let oneCred = Array.from(validCredentials).find(x => possibleCredentials.has(x))
        if (oneCred) {
            value[protocolId] = [oneCred]
        }
    }
}

function toggle (type: CredentialKind) {
    if (value[protocolId]!.includes(type)) {
        value[protocolId] = value[protocolId]!.filter((x: CredentialKind) => x !== type)
    } else {
        value[protocolId]!.push(type)
    }
}
</script>

<div class="d-flex wrapper">
    <Input
        id={'policy-editor-' + user.username + protocolId}
        type="switch"
        bind:checked={isAny}
        label="Any credential"
        on:change={updateAny}
    />
    {#if !isAny}
        {#each [...validCredentials] as type}
            {#if possibleCredentials.has(type)}
                <Input
                    id={'policy-editor-' + user.username + protocolId + type}
                    type="switch"
                    checked={value[protocolId]?.includes(type)}
                    label={labels[type]}
                    on:change={() => toggle(type)}
                />
            {/if}
        {/each}
    {/if}
</div>

<style lang="scss">
    .wrapper {
        flex-wrap: wrap;
        :global(.form-switch) {
            margin-right: 1rem;
        }
    }
</style>
