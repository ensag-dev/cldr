<template>
    <div id="home">
        My Session Id:
            <code>{{ cldrOpts.cldrStatus.getSessionId() }}</code>
            — <i>(for debugging, you can use this session id with the Swagger UI)</i>
        <br>
        Current Status Code: {{ statusData.status }}
        <button v-on:click="stop()">Stop</button>
        <button v-on:click="start()">Start</button>
        <br>
        Current Status Message:
            <span v-html="statusData.ret"></span>
        <br>
        Results:
            <span v-html="statusData.output"></span>
    </div>
</template>

<script>
export default {
    props: ["specialPage", "cldrOpts"],
    data: function() {
        return {
            statusData: {
                wait: "Loading…"
            }
        }
    },
    created: function() {
        this.fetchStatus();
    },
    methods: {
        fetchStatus: function() {
        const SECONDS_IN_MS = 1000;
        // timeouts
        const RETRY_ON_FETCH_ERR = 15 * SECONDS_IN_MS; // When we couldn't talk to server
        const NORMAL_RETRY       = 5 * SECONDS_IN_MS;  // "Normal" retry: starting or about to start
        const BUSTED_RETRY       = 60 * SECONDS_IN_MS; // ST is down
        const sessionId = this.cldrOpts.cldrStatus.getSessionId();
            fetch(`api/summary?ss=${sessionId}`, {
                method: 'POST',
                headers: {
                    'Content-Type': 'application/json'
                },
                body: JSON.stringify({
                    loadingPolicy: 'NOSTART'
                })
            })
            .then(r => r.json())
            .then(data => {
                this.statusData = data;
                window.setTimeout(this.fetchStatus.bind(this), NORMAL_RETRY);
            });
        },
        stop: function() {
        const sessionId = this.cldrOpts.cldrStatus.getSessionId();
            fetch(`api/summary?ss=${sessionId}`, {
                method: 'POST',
                headers: {
                    'Content-Type': 'application/json'
                },
                body: JSON.stringify({
                    loadingPolicy: 'FORCESTOP'
                })
            })
            .then(r => r.json())
            .then(data => {
                this.statusData = data;
            });
        },
        start: function() {
        const sessionId = this.cldrOpts.cldrStatus.getSessionId();
            fetch(`api/summary?ss=${sessionId}`, {
                method: 'POST',
                headers: {
                    'Content-Type': 'application/json'
                },
                body: JSON.stringify({
                    loadingPolicy: 'START'
                })
            })
            .then(r => r.json())
            .then(data => {
                this.statusData = data;
            });
        },
    }
}
</script>

<style scoped>


</style>