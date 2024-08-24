<template>
    <h1>RANDOM PASSWORD GENERATOR</h1>
    <br>
    <center>
        <div id="outer-div">
            <div class="container text-center">
                <div class="row">
                    <div class="col" style="margin-top: 10px;">
                        <b>INCLUDE CAPITAL LETTERS</b>
                    </div>
                    <div class="col">
                        <input type="checkbox" class="btn-check" id="btn-check-capital-letters" autocomplete="off"
                            v-model="includeUppercase" checked>
                        <label class="btn btn-outline-primary" for="btn-check-capital-letters">A-Z</label>
                    </div>
                </div>
            </div>
            <br>
            <div class="container text-center">
                <div class="row">
                    <div class="col" style="margin-top: 10px;">
                        <b>INCLUDE SMALL LETTERS</b>
                    </div>
                    <div class="col">
                        <input type="checkbox" class="btn-check" id="btn-check-small-letters" autocomplete="off"
                            v-model="includeLowercase" checked disabled>
                        <label class="btn btn-outline-primary" for="btn-check-small-letters">a-z</label>
                    </div>
                </div>
            </div>
            <br>
            <div class="container text-center">
                <div class="row">
                    <div class="col" style="margin-top: 10px;">
                        <b>INCLUDE SPECIAL CHARACTERS</b>
                    </div>
                    <div class="col">
                        <input type="checkbox" class="btn-check" id="btn-check-special-characters" autocomplete="off"
                            v-model="includeSymbols">
                        <label class="btn btn-outline-primary" for="btn-check-special-characters">!@#,.</label>
                    </div>
                </div>
            </div>
            <br>
            <div class="container text-center">
                <div class="row">
                    <div class="col" style="margin-top: 10px;">
                        <b>INCLUDE NUMBERS</b>
                    </div>
                    <div class="col">
                        <input type="checkbox" class="btn-check" id="btn-check-numbers" autocomplete="off"
                            v-model="includeNumbers">
                        <label class="btn btn-outline-primary" for="btn-check-numbers">0-9</label>
                    </div>
                </div>
            </div>
            <br>
            <div class="container text-center">
                <div class="row">
                    <div class="col" style="margin-top: 10px;">
                        <b>PASSWORD LENGTH</b>
                    </div>
                    <div class="col" style="padding-top: 5px;">
                        8 <input type="range" class="form-range" min="8" max="20" id="password-length" value="12"
                            style="width: 80%" v-model="passwordLength"> 20
                    </div>
                </div>
            </div>
            <br>
            <div class="container text-center">
                <div class="form-floating mb-3">
                    <input class="form-control" id="floatingInput" v-model="floatingInputPhrase"
                        @keydown.space="(event) => event.preventDefault()" maxlength="10">
                    <label for="floatingInput">Word / Phrase</label>
                </div>
            </div>
            <div class="container text-center">
                <div class="row">
                    <button type="button" class="btn btn-primary" @click="generatePassword">GENERATE PASSWORD</button>
                </div>
            </div>
            <br>
            <br>
            <div class="container text-center">
                <div class="card">
                    <div class="card-body">
                        <input type="text" v-model="generatedPassword" readonly style="width: 60%; margin-right: 2%">
                        <button id="copy-btn" class="btn btn-clipboard" style="border: groove; align: center"
                                    @click="copyPassword">COPY</button>
                    </div>
                </div>
            </div>
        </div>
        <br>
        <br>
        <div id="outer-desc-div">
            <h2>About Random Password Generator</h2>
            <br>
            <p>
                Basic Random Password Generator which allows Captial Letters, Small Letters, Digits and Special Characters. Based on using <b>Math.Random()</b> function from java script, will be selecting the number of characters for password from the above mentioned pool. Length of the characters can also be added.
            </p>
            <br>
            <p style="text-align: -webkit-auto;">
                Another Field is also added as an option to increase the password length. This section takes the phrase / word as input and tries to substitiue with alternatives.<br>
                    1. Alternatives could be Captial Letter if the input character is Small Letter and Vice-Versa.<br>
                    2. Alternatives could be Special Character if the input character looks similar. 
                    <br><b>Example:</b>  $(dollar syymbol) can be a good alternative for letter s.<br>
                    3. And finally gets appended with the randomised password.<br><br>
            </p>
        </div>
    </center>
</template>

<script>
import { characterAlternatives } from '../constant.js';

export default {
    name: 'RandomPasswordGenerator',
    props: {
        msg: String,
        isActive: Boolean,
    },
    data() {
        return {
            includeUppercase: true,
            includeLowercase: true,
            includeNumbers: true,
            includeSymbols: true,
            passwordLength: 8,
            generatedPassword: '',
            floatingInputPhrase: '',
            characterAlternatives: characterAlternatives
        };
    },
    methods: {
        generatePassword() {
            // Generate a random password based on selected options and length
            const lowercaseLetters = 'qpalzwoskxmeidjcnrufhvbtyg';
            const uppercaseLetters = lowercaseLetters.toUpperCase();
            const numbers = '0123456789';
            const symbols = '!@#$%^&*()_+-=[]{};:\'",./<>?|\\';
            let characterSet = '';

            if (this.includeUppercase) {
                characterSet += uppercaseLetters;
            }
            if (this.includeLowercase) {
                characterSet += lowercaseLetters;
            }
            if (this.includeNumbers) {
                characterSet += numbers;
            }
            if (this.includeSymbols) {
                characterSet += symbols;
            }

            this.generatedPassword = '';
            for (let i = 0; i < this.passwordLength; i++) {
                const randomIndex = Math.floor(Math.random() * characterSet.length);
                this.generatedPassword += characterSet[randomIndex];
            }
            
            this.transformedPhrase = '';
            if(this.floatingInputPhrase) {
                this.transformedPhrase = this.floatingInputPhrase.split('').map(char => {
                    if (characterAlternatives[char]) {
                        const alternatives = characterAlternatives[char];
                        if (alternatives.length > 1) {
                            const randomIndex = Math.floor(Math.random() * alternatives.length);
                            return alternatives[randomIndex];
                        } else {
                            return alternatives[0];
                        }
                    } else {
                        return char; // If character doesn't have an alternative, keep it unchanged
                    }
                }).join('');

                const randomSeparatorIndex = Math.floor(Math.random() * symbols.split('').length);
                this.separatorCharacter = symbols.split('')[randomSeparatorIndex]
                this.transformedPhrase =  this.separatorCharacter + this.transformedPhrase

            }

            // Final Password
            this.generatedPassword += this.transformedPhrase

        },
        copyPassword() {
            // Check if clipboard API is supported
            if (!navigator.clipboard) {
                const textArea = document.createElement('textarea');
                textArea.value = this.generatedPassword;
                textArea.style.position = 'fixed';
                textArea.style.left = '-9999px';
                textArea.style.top = '-9999px';
                document.body.appendChild(textArea);
                textArea.select();
                try {
                    document.execCommand('copy');
                    console.log('Password copied to clipboard (fallback method)');
                } catch (err) {
                    console.error('Failed to copy password:', err);
                } finally {
                    document.body.removeChild(textArea);
                }
            } else {
                // Use clipboard API to copy the password
                navigator.clipboard.writeText(this.generatedPassword)
                    .then(() => {
                        console.log('Password copied to clipboard!');
                    })
                    .catch(err => {
                        console.error('Failed to copy password:', err);
                    });
            }
        },
    }
}
</script>

<style scoped>
h1 {
    color: #19553a;
}

#outer-div {
    border-radius: 10%;
    padding: 2%;
    background-color: rgb(249, 247, 247);
    width: 75%;
}

#outer-desc-div {
    border-radius: 10%;
    padding: 2%;
    background-color: rgb(249, 247, 247);
    width: 75%;
}

</style>