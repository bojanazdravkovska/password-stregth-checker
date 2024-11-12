<template>
  <div id="app">
    <h1>Password Strength Checker</h1>
    <div class="input-container">
      <input v-model="email" placeholder="Enter your email" />
    </div>
    <div class="input-container">
      <input v-model="password" placeholder="Enter your password" @input="evaluatePassword" />
      <span class="message" v-if="msg">{{ msg }}</span>
    </div>
    <div v-if="passwordScore !== null">
      <p :class="passwordStrengthClass">Password Strength: {{ passwordStrength }}</p>
      <p>Score: {{ passwordScore }}/10</p>
    </div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      email: '',
      password: '',
      msg: '',
      commonPasswords: ['password', '12345', 'qwerty', 'admin', '123123', '12345678', 'abc123', '1111'],
      passwordScore: null,
      passwordStrength: '',
    };
  },
  computed: {
    passwordStrengthClass() {
      if (this.passwordStrength === 'Weak') {
        return 'weak';
      } else if (this.passwordStrength === 'Medium') {
        return 'medium';
      } else if (this.passwordStrength === 'Strong') {
        return 'strong';
      }
      return '';
    }
  },
  methods: {
    evaluatePassword() {
      this.msg = '';
      this.passwordScore = null;

      if (!this.password) {
        return;
      }

      let score = 0;

      if (this.password.length >= 8) {
        score += 1;
      }
      if (this.password.length >= 12) {
        score += 1;
      }

      const hasLower = /[a-z]/.test(this.password);
      const hasUpper = /[A-Z]/.test(this.password);
      const hasDigit = /\d/.test(this.password);
      const hasSpecial = /[!@#$%^&*(),.?":{}|<>]/.test(this.password);

      if (hasLower) score += 1;
      if (hasUpper) score += 1;
      if (hasDigit) score += 1;
      if (hasSpecial) score += 1;

      if (/[!@#$%^&*(),.?":{}|<>]/.test(this.password) && this.password.match(/[!@#$%^&*(),.?":{}|<>]/g).length > 1) {
        score += 1;
      }

      if (/^[!@#$%^&*(),.?":{}|<>]/.test(this.password) && /[!@#$%^&*(),.?":{}|<>]$/.test(this.password)) {
        score += 1;
      }

      let commonWordFound = false;
      for (let word of this.commonPasswords) {
        if (this.password.toLowerCase().includes(word)) {
          commonWordFound = true;
          break;
        }
      }
      if (!commonWordFound) score += 1;

      if (this.password.toLowerCase() !== this.email.toLowerCase()) {
        score += 1;
      }

      this.passwordScore = score;

      if (score < 7) {
        this.passwordStrength = 'Weak';
      } else if (score >= 7 && score <= 8) {
        this.passwordStrength = 'Medium';
      } else {
        this.passwordStrength = 'Strong';
      }

      if (commonWordFound) {
        this.msg = 'Password cannot contain common words.';
      } else if (this.password.toLowerCase() === this.email.toLowerCase()) {
        this.msg = 'Password cannot be the same as the email address.';
      } else if (this.password.length < 8) {
        this.msg = 'Password must be at least 8 characters long.';
      } else if (!hasLower || !hasUpper || !hasDigit || !hasSpecial) {
        this.msg = 'Password must contain at least one lowercase letter, one uppercase letter, one digit, and one special character.';
      }
    }
  }
};
</script>

<style scoped>
  #app {
    text-align: left;
    margin-top: 20px;
    padding: 20px;
  }
  .input-container {
    margin-bottom: 15px;
  }
  input {
    display: block;
    padding: 10px;
    width: 300px;
    margin-bottom: 5px;
  }
  .message {
    display: inline-block;
    margin-left: 10px;
    font-size: 14px;
    color: #555;
  }
  .weak {
    color: red;
  }
  .medium {
    color: yellow;
  }
  .strong {
    color: green;
  }
</style>
