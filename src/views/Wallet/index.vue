<template lang="pug">
  main
    .container
      .flex-container.space-between.header
        .flex-container
          img.sign-in__logo.d-inline-block(
            src="~assets/logoIcon.svg",
            alt="Wallet"
            width="80"
          )
          img.sign-in__logo.d-inline-block.imgText(
            src="~assets/logoText.svg",
            alt="Wallet: InventiStudio recruitment task"
          )
        .right
          button.o-btn.btn.btn-link(
            @click="signOut()",
            type="button",
          )
            span.fs-16.c-blackish Logout
      .flex-container.space-between
        div
          p 
          |  Your wallet's balance is 
          span.balance {{ transactions.map( (record) => record.amount ).reduce( (accumulator, currentValue) => accumulator + currentValue ) }}
          | .
        .right
          .buttons
            button.o-btn(
              @click="filterTrans(0)",
              type="button",
              class = "bg-blue",
            )
              span All
            
            button.o-btn(
              @click="filterTrans(1)",
              type="button",
              class = "bg-white"
            )
              span Show additions

            button.o-btn(
              @click="filterTrans(2)",
              type="button",
              class = "bg-white"
            )
              span Withdrawal
      .row.justify-content-center
        table
          thead.tableHeader
            tr
              th.thAdded Added at
              th.thTitle Title
              th Amount
              th.thMore
          tbody
            tr(v-for='transaction in filteredTransactions')
              td {{transaction.createdAt | formatDate }}
              td {{ transaction.name }}
              td.alignRight {{ transaction.amount }}
              td.alignRight 
                img(
                  src="~assets/more.svg",
                  alt="More"
                ).pointer
</template>

<script>
  import { mapActions } from 'vuex'
  import store from 'src/store'
  import router from 'src/router'
  import ls from 'local-storage'

  export default {
    name: 'Logout',
    data: () => ({
      filteredTransactions: [
        {
          amount: "",
          createdAt: "",
          id: "",
          name: "",
          updatedAt: "",
          userId: ""
        }],
      transactions: [{amount:"Loading"}],
    }),
    created() {
      this.isLogged = store.getters["auth/isLoggedIn"];
      if (this.isLogged ===  false) {
        router.push({ name: 'SignIn' })
      }
      else this.getTrans()
    },
    methods: {
      ...mapActions({
        logout: 'auth/logout',
        gettrans: 'auth/getTrans',
      }),
      async signOut() {
        try {
          await this.logout()
        } catch (err) {
          throw err
        }
      },
       async getTrans() {
        try {
          this.transactions = this.filteredTransactions = await this.gettrans()
        } catch (err) {
          throw err
        }
      },
      filterTrans: function (value) {
        if( value === 1 ) {
          this.val = value;
          this.filteredTransactions = this.transactions.filter( (tran) => { return tran.amount > 0})
        }
        else if( value === 2 ) {
          this.val = value;
          this.filteredTransactions = this.transactions.filter( (tran) => { return tran.amount < 0})
        }
        else {
          this.val = value;
          this.filteredTransactions = this.transactions
        }
      },
    },
    filters: {
      formatDate: function (value) {
        return value.substring(0,10)
      }
    }
  }
  
</script>

<style lang="sass" scoped>
  
    
</style>
