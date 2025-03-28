<script lang="ts">
import { serverInfo, reloadServerInfo } from 'gateway/lib/store'

import Router, { link } from 'svelte-spa-router'
import active from 'svelte-spa-router/active'
import { wrap } from 'svelte-spa-router/wrap'
import ThemeSwitcher from 'common/ThemeSwitcher.svelte'
import Logo from 'common/Logo.svelte'
import DelayedSpinner from 'common/DelayedSpinner.svelte'
import AuthBar from 'common/AuthBar.svelte'

async function init () {
    await reloadServerInfo()
}

init()

const routes = {
    '/': wrap({
        asyncComponent: () => import('./Home.svelte') as any,
    }),
    '/sessions/:id': wrap({
        asyncComponent: () => import('./Session.svelte') as any,
    }),
    '/recordings/:id': wrap({
        asyncComponent: () => import('./Recording.svelte') as any,
    }),
    '/tickets': wrap({
        asyncComponent: () => import('./Tickets.svelte') as any,
    }),
    '/tickets/create': wrap({
        asyncComponent: () => import('./CreateTicket.svelte') as any,
    }),
    '/config': wrap({
        asyncComponent: () => import('./Config.svelte') as any,
    }),
    '/targets/create': wrap({
        asyncComponent: () => import('./CreateTarget.svelte') as any,
    }),
    '/targets/:id': wrap({
        asyncComponent: () => import('./Target.svelte') as any,
    }),
    '/roles/create': wrap({
        asyncComponent: () => import('./CreateRole.svelte') as any,
    }),
    '/roles/:id': wrap({
        asyncComponent: () => import('./Role.svelte') as any,
    }),
    '/users/create': wrap({
        asyncComponent: () => import('./CreateUser.svelte') as any,
    }),
    '/users/:id': wrap({
        asyncComponent: () => import('./User.svelte') as any,
    }),
    '/ssh': wrap({
        asyncComponent: () => import('./SSH.svelte') as any,
    }),
    '/log': wrap({
        asyncComponent: () => import('./Log.svelte') as any,
    }),
}
</script>

{#await init()}
    <DelayedSpinner />
{:then}
    <div class="app container">
        <header>
            <a href="/@warpgate" class="d-flex">
                <div class="logo">
                    <Logo />
                </div>
            </a>
            {#if $serverInfo?.username}
                <a use:link use:active href="/">Sessions</a>
                <a use:link use:active href="/config">Config</a>
                <a use:link use:active href="/tickets">Tickets</a>
                <a use:link use:active href="/ssh">SSH</a>
                <a use:link use:active href="/log">Log</a>
            {/if}
            <AuthBar />
        </header>
        <main>
            <Router {routes}/>
        </main>

        <footer class="mt-5">
            <span class="me-auto">
                v{$serverInfo?.version}
            </span>
            <ThemeSwitcher />
        </footer>
    </div>
{/await}

<style lang="scss">
    .app {
        min-height: 100vh;
        display: flex;
        flex-direction: column;
    }

    .logo {
        width: 40px;
        padding-top: 2px;
        display: flex;
    }

    header, footer {
        flex: none;
    }

    main {
        flex: 1 0 0;
    }

    header {
        display: flex;
        align-items: center;
        padding: 10px 0;
        margin: 10px 0 20px;

        a, .logo {
            font-size: 1.5rem;
        }

        a:not(:first-child) {
            margin-left: 15px;
        }
    }
</style>
