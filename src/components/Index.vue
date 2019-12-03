<template>
<div>
  <div class="section has-background-black-bis is-fullheight">
    <div class="container">
      <div class="notification has-background-black-ter has-text-light">
        <div class="level">
          <h1 class="title">Regex pa' la banda!</h1>
        </div>
        <div class="field">
          <label class="label has-text-light" v-bind:class="{'has-text-danger': hasError, 'has-text-success': hasText}">Regex</label>
          <div class="control has-icons-right">
            <input class="input" v-bind:class="{'is-danger': hasError, 'is-success': hasText}" type="text" placeholder="$.*" name="regex_id" id="regex-id" v-model="regexId" @input="process($event)">
            <template v-if="hasError" v-show="hasError">
              <span  class="icon is-small is-right">
                <i class="fas fa-exclamation-triangle has-text-danger"></i>
              </span>
            </template>
            <template v-if="hasText" v-show="hasText">
              <span  class="icon is-small is-right">
                <i class="fas fa-check has-text-success"></i>
              </span>
            </template>
          </div>
        </div>

        <div class="field">
          <label class="label has-text-light">Texto</label>
          <div class="control">
            <textarea class="textarea" placeholder="Lorem ipsum dolor sit amet." v-model="searchStr" @input="process($event)"></textarea>
          </div>
        </div>

        <br>
        <div class="field">
          <label class="label has-text-light">Coincidencias</label>
        </div>

        <div class="level">
          <span v-html="match"></span>
        </div>
      </div>
    </div>



  </div>
  <footer class="footer has-background-black-bis">
    <div class="content has-text-centered">
      <p>
        <strong>Made with ❤️</strong> by <a href="https://github.com/carlosrivera">Carlos Rivera</a>. The source code is licensed
        <a href="http://opensource.org/licenses/mit-license.php">MIT</a>. The website content
        is licensed <a href="http://creativecommons.org/licenses/by-nc-sa/4.0/">CC BY NC SA 4.0</a>.
      </p>
    </div>
  </footer>
</div>
</template>

<script>
export default {
  name: 'Index',
  data () {
    return {
      regexId: '',
      searchStr: '',
      hasError: false,
      match: '',
    }
	},
  computed: {
    hasText: function () {
      return (this.regexId.length && this.hasError === false) ? true : false;
    },
    query () {
      // We will see what `params` is shortly
      return this.$route.params.q
    }
  },
	methods: {
		process: function(event) {
      try {
        var pattern = new RegExp(this.regexId);

        let m = pattern.exec(this.searchStr);

        if (m !== null) {
          m.forEach((match, groupIndex) => {
            console.log(`Found match, group ${groupIndex}: ${match}`);
          });

          this.match = this.searchStr.replace(
            new RegExp(this.regexId, 'gmu'),
            function(str) {
              return '<b class="has-text-info has-background-grey-dark">'+str+'</b>'
            }
          );
        } else {
          this.match = "No se encontro un match."
        }

        this.hasError = false;
      }
      catch(err) {
        this.hasError = true;
        this.match = "El regex no es válido."
      }

      this.$router.push({
        name: 'IndexQuery',
        params: {
          q: btoa(unescape(encodeURIComponent(this.regexId + '<<#>>' + this.searchStr)))
        }
      });
		}
	},
  mounted: function () {
    var str = decodeURIComponent(escape(atob(this.query))).split('<<#>>');

    this.regexId = str[0];
    this.searchStr = str[1];

    this.process(null);
  },
  watch: {
    '$route' (to, from) {
      console.log(this.hasError, this.hasText);
    }
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
