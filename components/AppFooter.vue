<template>
    <div class="AppFooter">
        <br />
        Copyright © 2024 - Made with vapes in Toulouse, France - V{{appVersion}}
    </div>
</template>

<script>
import { version } from '../package.json';
export default {
  data: () => ({
    appVersion: version
  })
};
</script>

<style>
.AppFooter {
    font-family: "Quicksand", "Source Sans Pro", -apple-system,
        BlinkMacSystemFont, "Segoe UI", Roboto, "Helvetica Neue", Arial,
        sans-serif;
    font-size: 0.8rem;
    color: white;
    font-weight: 300;
    margin: 2px;
    text-align: center;
}
</style>
