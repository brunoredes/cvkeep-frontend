<template>
  <fieldset class="field" aria-labelledby="findACv">
    <label for="subject" id="findACv">
      <i class="fa fa-search" aria-hidden="true" role="presentation"></i>
      <span> {{ $t('searchMessages.findACv') }}</span>
    </label>

    <div class="search-input-holder" :disabled="disabled">
      <input type="text" :name="name || 'subject'"
				:id="name || 'subject'"
				:placeholder="$t('searchMessages.nameRoleOrLocation')"
				:value="value"
				:disabled="disabled"
				:aria-placeholder="$t('searchMessages.nameRoleOrLocation')"
				:aria-disabled="disabled"
				maxlength="54">

      <button type="submit" :aria-label="$t('searchMessages.findACv')">
        <i class="fa fa-arrow-right" role="presentation" aria-hidden="true"></i>
      </button>
    </div>
  </fieldset>
</template>

<script>
  export default {
    name: 'search-simple-input',

    props: {
      name: {
        type: String,
        default: ''
      },

      value: {
        type: String,
        default: ''
      },

      disabled: {
        type: Boolean,
        deafult: false
      }
    }
  }
</script>

<style lang="scss" scoped="true">
  .field {
    margin-bottom: 8px;
    label {
      font-size: 24px;
      color: var(--primary-color);
    }
    .search-input-holder {
      position: relative;
      input {
        height: auto;
        margin: 0;
        font-size: 32px;
        border-radius: 36px;
        padding: var(--gutter) 36px;
        padding-right: 100px;
        background-color: #fff;
        border: solid 2px var(--color-gray-light);
      }
      button {
        right: 0;
        top: 50%;
        height: 100%;
        padding: 0;
        width: 90px;
        position: absolute;
        border-radius: 36px;
        transform: translateY(-50%);
        border: none;
        border-top-left-radius: 0;
        border-bottom-left-radius: 0;
        border: solid 2px var(--color-gray-light);
        font-size: 20px;
      }
    }
  }
  @media screen and (max-width: 768px) {
    .field {
      padding: 0 var(--gutter);
      .search-input-holder {
        input {
          font-size: 18px;
          padding-right: 72px;
        }
        button {
          font-size: 18px;
          width: 56px;
        }
      }
    }
  }
</style>
