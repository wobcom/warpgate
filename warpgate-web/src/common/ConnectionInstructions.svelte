<script lang="ts">
    import { FormGroup } from '@sveltestrap/sveltestrap'
    import { TargetKind } from 'gateway/lib/api'
    import { serverInfo } from 'gateway/lib/store'
    import { makeExampleSSHCommand, makeSSHUsername, makeExampleMySQLCommand, makeExampleMySQLURI, makeMySQLUsername, makeTargetURL, makeExamplePostgreSQLCommand, makePostgreSQLUsername, makeExamplePostgreSQLURI } from 'common/protocols'
    import CopyButton from 'common/CopyButton.svelte'
    import Alert from './Alert.svelte'

    interface Props {
        targetName?: string;
        targetKind: TargetKind;
        targetExternalHost?: string;
        username?: string;
        ticketSecret?: string;
    }

    let {
        targetName,
        targetKind,
        targetExternalHost = undefined,
        username,
        ticketSecret = undefined,
    }: Props = $props()

    let opts = $derived({
        targetName,
        username,
        serverInfo: $serverInfo,
        ticketSecret,
        targetExternalHost,
    })
    let sshUsername = $derived(makeSSHUsername(opts))
    let exampleSSHCommand = $derived(makeExampleSSHCommand(opts))
    let mySQLUsername = $derived(makeMySQLUsername(opts))
    let exampleMySQLCommand = $derived(makeExampleMySQLCommand(opts))
    let exampleMySQLURI = $derived(makeExampleMySQLURI(opts))
    let postgreSQLUsername = $derived(makePostgreSQLUsername(opts))
    let examplePostgreSQLCommand = $derived(makeExamplePostgreSQLCommand(opts))
    let examplePostgreSQLURI = $derived(makeExamplePostgreSQLURI(opts))
    let targetURL = $derived(targetName ? makeTargetURL(opts) : '')
    let authHeader = $derived(`Authorization: Warpgate ${ticketSecret}`)
</script>

{#if targetKind === TargetKind.Ssh}
    <FormGroup floating label="SSH username" class="d-flex align-items-center">
        <input type="text" class="form-control" readonly value={sshUsername} />
        <CopyButton text={sshUsername} />
    </FormGroup>

    <FormGroup floating label="Example command" class="d-flex align-items-center">
        <input type="text" class="form-control" readonly value={exampleSSHCommand} />
        <CopyButton text={exampleSSHCommand} />
    </FormGroup>
{/if}

{#if targetKind === TargetKind.Http}
    <FormGroup floating label="Access URL" class="d-flex align-items-center">
        <input type="text" class="form-control" readonly value={targetURL} />
        <CopyButton text={targetURL} />
    </FormGroup>

    {#if ticketSecret}
        Alternatively, set the <code>Authorization</code> header when accessing the URL:
        <FormGroup floating label="Authorization header" class="d-flex align-items-center">
            <input type="text" class="form-control" readonly value={authHeader} />
            <CopyButton text={authHeader} />
        </FormGroup>
    {/if}
{/if}

{#if targetKind === TargetKind.MySql}
    <FormGroup floating label="MySQL username" class="d-flex align-items-center">
        <input type="text" class="form-control" readonly value={mySQLUsername} />
        <CopyButton text={mySQLUsername} />
    </FormGroup>

    <FormGroup floating label="Example command" class="d-flex align-items-center">
        <input type="text" class="form-control" readonly value={exampleMySQLCommand} />
        <CopyButton text={exampleMySQLCommand} />
    </FormGroup>

    <FormGroup floating label="Example database URL" class="d-flex align-items-center">
        <input type="text" class="form-control" readonly value={exampleMySQLURI} />
        <CopyButton text={exampleMySQLURI} />
    </FormGroup>

    <Alert color="info">
        Make sure you've set your client to require TLS and allowed cleartext password authentication.
    </Alert>
{/if}

{#if targetKind === TargetKind.Postgres}
<FormGroup floating label="PostgreSQL username" class="d-flex align-items-center">
    <input type="text" class="form-control" readonly value={postgreSQLUsername} />
    <CopyButton text={postgreSQLUsername} />
</FormGroup>

<FormGroup floating label="Example command" class="d-flex align-items-center">
    <input type="text" class="form-control" readonly value={examplePostgreSQLCommand} />
    <CopyButton text={examplePostgreSQLCommand} />
</FormGroup>

<FormGroup floating label="Example database URL" class="d-flex align-items-center">
    <input type="text" class="form-control" readonly value={examplePostgreSQLURI} />
    <CopyButton text={examplePostgreSQLURI} />
</FormGroup>

<Alert color="info">
    Make sure you've set your client to require TLS and allowed cleartext password authentication.
</Alert>
{/if}
