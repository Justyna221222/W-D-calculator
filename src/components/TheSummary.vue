<template>
<div class="summary">
    <table class="table table-striped">
        <tbody>
        <tr class="highlighted">
            <th>Element okna</th>
            <th>Wybrano</th>
        </tr>  
        <tr>
            <td>Typ</td>
            <td>{{ this.$store.state.winType }}</td>
        </tr>  
        <tr>
            <td>Rozmiar</td>
            <td>{{ this.$store.state.width }} x {{this.$store.state.height }}</td>
        </tr>
        <tr v-if="isVisible">
            <td>Profil</td>
            <td>{{ this.$store.state.winProfile }}</td>
        </tr>
        <tr v-if="isVisible">
            <td>Okleina</td>
            <td>{{ this.$store.state.winVenner }}</td>
        </tr>
        <tr v-if="isVisible">
            <td>Szyba</td>
            <td>{{ this.$store.state.winGlass }}</td>
        </tr>
        <tr v-if="isVisible">
            <td>Ciepłe ramki</td>
            <td>{{ this.$store.state.winFrame }}</td>
        </tr>
        <tr v-if="isVisible">
            <td>Szprosy</td>
            <td>{{ this.$store.state.winMuntins }}</td>
        </tr>
        <tr v-if="isVisible">
            <td>Nawiewniki</td>
            <td>{{ this.$store.state.winDiffuser }}</td>
        </tr>
        <tr v-if="isVisible">
            <td>Roleta</td>
            <td>{{ this.$store.state.winBlind }}</td>
        </tr>
        <tr>
            <td>Podsumowanie</td>
            <td>{{ this.$store.state.winBlindPrice }}</td>
        </tr>
        </tbody>
    </table>
    <div>
        <p class="big">Gratulujemy! Cena Twojego okna jest już obliczona. Jeżeli chcesz ją poznać, wyślemy Ci wiadomość na Twój adres e-mail z obliczonymi specjalnymi RABATAMI !</p> 
        <p class="small">To nie jest zapis na newsletter. Nie będziemy przysyłać Ci żadnych seryjnych wiadomości.</p>      
    </div>

<div>
    <p>E-mail</p>
    <input id='yourName' type='email' v-model='yourEmail' @blur="validateEmail">
    <p v-if="invalidEmail" class="warning">Wprowadź adres e-mail</p>     
</div >
<div class="buttons">
    <div>
        <button @click="submit">Wyślij cenę okna</button>
    </div>
    <div>
        <button @click="addItem">Dodaj kolejną pozycję</button>
    </div>
    <div>
        <button @click="fromBeginning">Oblicz od początku</button>
    </div>
</div>
<div>{{this.$store.state.Windows}}</div>
<div v-if="emailSend" class="emailSend">
    <p>Wiadomość została wysłana, dziękujemy!</p>
</div>
<div v-if="errSend">
    <p>Coś poszło nie tak, spróbuj jeszcze raz!</p>
</div>
</div>



</template>
<script>
import emailjs from '@emailjs/browser';
import store from '../store/index.js';  // import the store 
import axios from 'axios'; 
export default {
    data() {
        return {
            yourEmail: '',
            emailSend: false,
            errSend: false,
            invalidEmail: false,
            mojeOkna: []

        };
    },
    computed: {
        isVisible() {
            const winType = this.$store.state.winType;
            if(winType == 'Drzwi jednoskrzydłowe 1' || winType == 'Drzwi jednoskrzydłowe 2' || winType == 'Drzwi dwuskrzydłowe PVC') {
                return false;
            } else {
                return true;
            }
        }
    },
    methods: {
            submit() {
      axios.post('../../mailer.php', {
                'okna': this.mojeOkna,
                'email': this.yourEmail              
        }).then(response => {
                console.log('success', response.data.message)                   
        }).catch(error => {
              console.log(error.response)
     }); 
},
        addItem() {
            const window = {
                typ: this.$store.state.winType,
                szerokość: this.$store.state.width,
                wysokość: this.$store.state.height,
                profil: this.$store.state.winProfile,
                okleina: this.$store.state.winVenner,
                szyby: this.$store.state.winGlass,
                ramki: this.$store.state.winFrame,
                szprosy: this.$store.state.winMuntins,
                nawiewniki: this.$store.state.winDiffuser,
                rolety: this.$store.state.winBlind,
                cena: this.$store.state.winBlindPrice,
            }
            console.log(window);
            console.log(window.typ);

            this.$store.commit('addNewWindow', window);
 /* Clear store to add new window*/
            this.$store.state.winType = '',
            this.$store.state.width = '',
            this.$store.state.height = '',
            this.$store.state.winSizePrice = '',            
            this.$store.state.winArea = '',
            this.$store.state.winProfile = '',
            this.$store.state.winProfilePrice = '',
            this.$store.state.winVenner = '',
            this.$store.state.winVennerPrice = '',
            this.$store.state.winGlass = '',
            this.$store.state.winGlassPrice = '',
            this.$store.state.winFrame = '',
            this.$store.state.winFramePrice = '',
            this.$store.state.winMuntins = '',
            this.$store.state.winMuntinsPrice = '',
            this.$store.state.winDiffuser = '',
            this.$store.state.winDiffuserPrice = '',
            this.$store.state.winBlind = '',
            this.$store.state.winBlindPrice = '',
            this.$store.state.basketDisabled = false
            this.yourEmail = '',
            this.$router.push("/windowType");
        },
        sendEmail() {

            const window = {
                typ: this.$store.state.winType,
                szerokość: this.$store.state.width,
                wysokość: this.$store.state.height,
                profil: this.$store.state.winProfile,
                okleina: this.$store.state.winVenner,
                szyby: this.$store.state.winGlass,
                ramki: this.$store.state.winFrame,
                szprosy: this.$store.state.winMuntins,
                nawiewniki: this.$store.state.winDiffuser,
                rolety: this.$store.state.winBlind,
                cena: this.$store.state.winBlindPrice,
            }

            this.$store.commit('addNewWindow', window);

            if(this.yourEmail == ''){
                this.invalidEmail = true;
                return;
            } else {
                const Windows = this.$store.state.Windows; 

                var parsed = '';
                for (let i = 0; i < Windows.length; i++) {
                 var Okna = Windows[i];
                    for (var property in Okna) {
                        parsed += property + ' : ' + Okna[property] + '\r\n' + '<br>';
                   }
                   parsed += '<br><br><br>';
              }
              
                this.mojeOkna = parsed;


        }

const templateParams = {
    Okna: this.mojeOkna,
    yourEmail: this.yourEmail

};
emailjs.send('service_u5bo6u3','template_7qaf81v', templateParams, 'Bk9OW_oJI8NKfrxhJ')
.then((response) => {
this.emailSend = true;
console.log('SUCCESS!', response.status, response.text);
}, (err) => {
this.errSend = true;
console.log('FAILED...', err);
});

setTimeout(() => {
            this.$router.push("/windowType");            
            this.$store.state.winType = '',
            this.$store.state.width = '',
            this.$store.state.height = '',
            this.$store.state.winSizePrice = '',            
            this.$store.state.winArea = '',
            this.$store.state.winProfile = '',
            this.$store.state.winProfilePrice = '',
            this.$store.state.winVenner = '',
            this.$store.state.winVennerPrice = '',
            this.$store.state.winGlass = '',
            this.$store.state.winGlassPrice = '',
            this.$store.state.winFrame = '',
            this.$store.state.winFramePrice = '',
            this.$store.state.winMuntins = '',
            this.$store.state.winMuntinsPrice = '',
            this.$store.state.winDiffuser = '',
            this.$store.state.winDiffuserPrice = '',
            this.$store.state.winBlind = '',
            this.$store.state.winBlindPrice = '',
            this.$store.state.basketDisabled = false,
            this.$store.state.Windows = [],
            this.yourEmail = ''    
}, 5000);


            },

        validateEmail() {
                if(this.yourEmail == '') {
                    this.invalidEmail = true;
                } else {
                    this.invalidEmail = false;
                }
            }, 
        fromBeginning() {
            this.$store.state.winType = '',
            this.$store.state.width = '',
            this.$store.state.height = '',
            this.$store.state.winSizePrice = '',            
            this.$store.state.winArea = '',
            this.$store.state.winProfile = '',
            this.$store.state.winProfilePrice = '',
            this.$store.state.winVenner = '',
            this.$store.state.winVennerPrice = '',
            this.$store.state.winGlass = '',
            this.$store.state.winGlassPrice = '',
            this.$store.state.winFrame = '',
            this.$store.state.winFramePrice = '',
            this.$store.state.winMuntins = '',
            this.$store.state.winMuntinsPrice = '',
            this.$store.state.winDiffuser = '',
            this.$store.state.winDiffuserPrice = '',
            this.$store.state.winBlind = '',
            this.$store.state.winBlindPrice = '',
            this.$store.state.basketDisabled = false,
            this.$store.state.Windows = [],
            this.yourEmail = '',
            this.$router.push("/windowType");
        }
    },
    beforeRouteEnter(to, from, next){
                const winBlind = store.state.winBlind;
                const winType = store.state.winType;
                var letIn;
                if(winBlind == '' && winType == 'Drzwi jednoskrzydłowe 1' || winType == 'Drzwi jednoskrzydłowe 2' || winType == 'Drzwi dwuskrzydłowe PVC') {
                    letIn = true;
                } else if(winBlind == '') {
                    letIn = false;
                } else {
                    letIn = true;
                }         
                next( letIn);
            },


}
</script>

<style scoped>
.summary {
    font-size: 16px;
}
table {
    border: 1px solid black;
}
.highlighted {
  background-color: #f3c2bd;
}
table {
    margin: 0;
    padding: 0;
}
.big {
    font-size: 19px;
    font-weight: bold;
    color: #cb2c2c;
    margin: 20px 0 0 0;
}
.small {
    font-size: 15px;
}
.emailSend p{
    color: green;
    font-size: 18px;
    font-weight: bold;
}
.buttons {
    display: flex;
    flex-direction: row;
    justify-content: space-between;
    padding: 0;
    margin: 0;
}
button {
  padding: 0.75rem 1.5rem;
  width: 300px;
  font-family: inherit;
  background-color: #cb2c2c;
  border: 1px solid #3a0061;
  color: white;
  cursor: pointer;
}

button:hover,
button:active {
  background-color: #690f0f;
  border-color: #270041;
}
.warning {
    color: red;
}
</style>
