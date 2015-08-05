---
layout: post
title: ES2015, string includes
---

Today I wanted to talk you about a little but useful feature in *ECMAScript 2015* (aka ES6).

I'm using the new Javascript in my pet project, so far it's been a much nicer experience. Currently I'm working with the registration screen, here is how it looks so far:

![Image description](/images/registrationES6includes.jpg)

Here is the ReactJS component that renders the form:

{% highlight js %}
'use strict';
import React from 'react';

let RegistrationForm = React.createClass({
    handleNext() {
        //Need to implement this
    },    

    handleUserNameEntered(e) {
       this.setState({userName: e.target.value});
    },

    handlePasswordEntered(e) {
       this.setState({password: e.target.value});
    },

    handlePasswordConfirmEntered(e) {
       this.setState({passwordConfirm: e.target.value});
    },

    handleEmailEntered(e) {
       this.setState({email: e.target.value});
    },

    getInitialState() {
        return {
            email: '',
            password: '',
            passwordConfirm: '',
            userName: ''
        };
    },

    render() {
        return (                    
                <form id="register-form" className="form-signin registration-form" >
                    <h2 className="form-signin-heading">register</h2>
                    <div className="main-login-form">
                        <div className="login-group">
                            //All the fields
                            <div className="form-group">
                                <input type="text" className="form-control" id="reg_email" name="reg_email" placeholder="email" onChange={this.handleEmailEntered} />
                            </div>
                        </div>
                    </div>
                    <a className="btn btn-lg btn-primary btn-block" onClick={this.handleNext}>Next</a>
                </form>
        );
    }
});

export default RegistrationForm;
{% endhighlight %}

When the user clicks **Next** I want to do some simple validations in the client side. I have already appended an **onClick** event listener and I have an event handler **handleNext**. There is where I want to carry out the validations.

One of them is checking that the email has the right format, including checking that includes an *@*.
If this was ES5 I would have to use this familiar structure:

{% highlight js %}
handleNext() {
	if(this.state.email.indexOf('@') !== -1) {
		console.log('Password valid');
	}
}
{% endhighlight %}

This code is simple but it is not very readable, *indexOf* returns *-1* if the substring is not present. If you know CSharp *string.Contains* method, now we can use a similar one in ES2015:

{% highlight js %}
handleNext() {
	if(this.state.email.includes('@') {
		console.log('Password valid');
	}
}
{% endhighlight %}

It is not a big deal but ES2015 is full of nice little features that will make our code overall simpler and more readable.
The really good thing of ES2015 is that you can start using it together with your usual ES5, **Babel** transpiler makes it all possible: it will transform to ES5 the code written in ES2015 while keeping the ES5 structures that it finds out.
