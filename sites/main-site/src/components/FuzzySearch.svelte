<script lang="ts">
    import fuzzysort from "fuzzysort";

    interface Props {
        possibleResults?: { href: string; name: string }[];
        placeholder?: string;
    }

    let { possibleResults = [], placeholder = "Search" }: Props = $props();

    let results = $state([]);

    function search(event: KeyboardEvent) {
        const input = event.target as HTMLInputElement;
        const value = input.value;
        if (value) {
            // search for the value
            results = fuzzysort.go(value, possibleResults, {
                key: "name",
            });
        } else {
            results = [];
        }
    }
</script>

<div class="search-container dropdown">
    <input id="fuzzy-search" class="form-control" type="text" {placeholder} aria-label={placeholder} onkeyup={search} />

    <ul class="search-results dropdown-menu" class:show={results.length > 0}>
        {#if results}
            {#each results as result (result)}
                <li>
                    <a href={result.obj.href} id={result.obj.name} class="text-decoration-none text-body dropdown-item">
                        {@html fuzzysort.highlight(result, '<span class="text-success">', "</span>")}
                    </a>
                </li>
            {/each}
        {/if}
    </ul>
</div>

<style lang="scss">
    .search-container {
        width: 100%;
        .dropdown-menu {
            min-width: 15rem;
        }
    }
</style>
