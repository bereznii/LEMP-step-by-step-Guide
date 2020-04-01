# LEMP install on Ubuntu 18.04

After around setting up LAMP and LEMP on multiple servers, I found for myself a set of perfectly working guides. Googling same things each time is irretional, so I decided to build a step-by-step guide for myself. Maybe it will be useful for someone.

## Getting Started

These instructions will get you through each step of installation process of LEMP (Linux, Nginx, MySQL and PHP) stack on clean machine with Ubuntu 18.04. Also see 'Laravel App Deployment' for guide to deploy laravel app easily.

### Setup of users in Ubuntu

Working as root is a bad practice, so I prefer to create new user and add him to sudo group.

```
$ adduser dev
$ usermod -aG sudo dev
$ usermod -aG www-data dev
```

Also, I'm turning off pubkey authentication, and turn on password one to be able enter server from different machines easily.

```
$ sudo nano /etc/ssh/sshd_config
```

In opened file make sure you have following lines:
* PasswordAuthentication yes
* PubkeyAuthentication no
* PermitRootLogin yes

```
$ sudo service sshd restart
```

Very important to not forget add password to your root user!

```
$ passwd
```

### Installing

A step by step series of examples that tell you how to get a development env running

Say what the step will be

```
Give the example
```

And repeat

```
until finished
```

End with an example of getting some data out of the system or using it for a little demo

## Running the tests

Explain how to run the automated tests for this system

### Break down into end to end tests

Explain what these tests test and why

```
Give an example
```

### And coding style tests

Explain what these tests test and why

```
Give an example
```

## Deployment

Add additional notes about how to deploy this on a live system

## Built With

* [Dropwizard](http://www.dropwizard.io/1.0.2/docs/) - The web framework used
* [Maven](https://maven.apache.org/) - Dependency Management
* [ROME](https://rometools.github.io/rome/) - Used to generate RSS Feeds

## Contributing

Please read [CONTRIBUTING.md](https://gist.github.com/PurpleBooth/b24679402957c63ec426) for details on our code of conduct, and the process for submitting pull requests to us.

## Versioning

We use [SemVer](http://semver.org/) for versioning. For the versions available, see the [tags on this repository](https://github.com/your/project/tags). 

## Authors

* **Billie Thompson** - *Initial work* - [PurpleBooth](https://github.com/PurpleBooth)

See also the list of [contributors](https://github.com/your/project/contributors) who participated in this project.

## License

This project is licensed under the MIT License - see the [LICENSE.md](LICENSE.md) file for details

## Acknowledgments

* Hat tip to anyone whose code was used
* Inspiration
* etc
