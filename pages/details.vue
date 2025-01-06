<template>
    <div class="container">
        <div class="containerCountry mb-5">
            <div class="country-header " v-if="country_detail" id="countryName">
                {{ country_detail.name.common }}
            </div>
            <div class="details row g-4" v-if="country_detail">
                <!-- Flag Section -->
                <div class="flag col-12 col-md-4 " id="countryFlag">
                    <img :src="country_detail.flags.png || ''" :alt="`${country_detail.name.common} flag`"
                        class="img-fluid w-75" />
                </div>
                <!-- Info Section -->
                <div class="country-info col-12 col-md-6">
                    <table class="table table-striped">
                        <tr>
                            <td>Native:</td>
                            <td>{{ country_detail.name.nativeName ?
                                Object.values(country_detail.name.nativeName)[0]?.common : "N/A" }}</td>
                        </tr>
                        <tr>
                            <td>Capital:</td>
                            <td>{{ country_detail.capital?.[0] || "N/A" }}</td>
                        </tr>
                        <tr>
                            <td>Population:</td>
                            <td>{{ country_detail.population.toLocaleString() }}</td>
                        </tr>
                        <tr>
                            <td>Region:</td>
                            <td>{{ country_detail.region }}</td>
                        </tr>
                        <tr>
                            <td>Sub-region:</td>
                            <td>{{ country_detail.subregion || "N/A" }}</td>
                        </tr>
                        <tr>
                            <td>Area:</td>
                            <td>{{ country_detail.area.toLocaleString() }} Km²</td>
                        </tr>
                        <tr>
                            <td>Country Code:</td>
                            <td>+{{ country_detail.idd?.root || "" }}{{ country_detail.idd?.suffixes?.[0] || "" }}</td>
                        </tr>
                        <tr>
                            <td>Languages:</td>
                            <td>{{ Object.values(country_detail.languages).join(" and ") || "N/A" }}</td>
                        </tr>
                        <tr>
                            <td>Currencies:</td>
                            <td>{{ Object.values(country_detail.currencies).map(c => c.name).join(", ") || "N/A" }}</td>
                        </tr>
                        <tr>
                            <td>Timezones:</td>
                            <td>{{ country_detail.timezones.join(", ") }}</td>
                        </tr>
                    </table>
                </div>
            </div>
        </div>


        <div class="containerNeighbour border p-4 rounded" v-if="neighbour_flags.length">
            <h2 class=" mb-4">Neighbouring Countries</h2>
            <div class="row row-cols-2 row-cols-md-3 g-3  d-flex align-items-center justify-content-center">
                <div v-for="(flag, index) in neighbour_flags" :key="index" class="col">
                    <div class="neighbour-flag">
                        <img :src="flag" alt="Neighbouring Country Flag" class="img-fluid rounded shadow" />
                    </div>
                </div>
            </div>
        </div>
    </div>
</template>

<script>
export default {
    data() {
        return {
            country_detail: null, // Initialize country_detail as null
            neighbour_flags: [], // To hold the flags of neighbouring countries
        };
    },
    async created() {
        const countryCode = this.$route.query.code;
        if (!countryCode) {
            return;
        }

        try {
            const response = await fetch(`https://restcountries.com/v3.1/alpha/${countryCode}`);
            const data = await response.json();
            this.country_detail = data[0]; // Set the fetched country details

            // Fetch the neighboring countries flags
            if (this.country_detail.borders && this.country_detail.borders.length > 0) {
                const neighbourPromises = this.country_detail.borders.map(border =>
                    fetch(`https://restcountries.com/v3.1/alpha/${border}`)
                        .then(res => res.json())
                        .then(neighbour => neighbour[0].flags.png)
                );

                const flags = await Promise.all(neighbourPromises);
                this.neighbour_flags = flags;
            }
        } catch (error) {
            console.error("Error fetching country details:", error);
            this.country_detail = null;
            this.neighbour_flags = [];
        }
    },
};
</script>

<style scoped>
.containerCountry {
    margin-bottom: 30px;
}

.country-header {
    font-size: 30px;
    font-weight: bold;
    text-align: center;
}

.details {
    display: flex;
    justify-content: start;
    align-items: center;
    gap: 20px;
}

.flag img {
    max-width: 700px;
    height: auto;
}

.containerNeighbour {
    border: 2px solid #ddd;
    /* Border around neighbour container */
}

.neighbour-flag img {
    width: 100%;
    height: auto;
    border-radius: 5px;
    box-shadow: 2px 2px 10px rgba(0, 0, 0, 0.1);
}
</style>
