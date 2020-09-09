# ***Personal details***
- Firstname:  ___Apisit___ Lastname: ___Sripar___ Nickname:  ___OaT___ Age: __22__ year old
- Contact number
    - ___090-921-4023___
- Gender
    - ___Male___
- Date of birth
    - ___18 Dec. 1997___
- Email 
    - ___oat19122@gmail.com___
- Social
    - [facebook](https://web.facebook.com/apisit.seepar/)
        
# ***Education*** 
- Institute ___Mahasarakham University.___
- Education level ___Degree.___
- Major ___Business Computer.___
- Graduation Year ___2019.___

# ***Work experience***
- Company ___WORLD MONEY LTD.___
- Position ___Backend developer (Web application)___
- Employment period ___May 2020 - Present___
- Job function ___Sofyware Development.___
- Job industry ___Financial Services.___
- Roles and responsibilities
    - ***Create RESTFUL API***
    - ***Studying Blockchain*** (Starting to study the ***Smart contract***)
    
# ***Languages***
- Thai 
- English(Fair)

# ***Language and Freamwork***
- **Language**
    - JavaScript
    - Python
    - HTML
    - CSS
    - PHP
- **Freamwork**
    - Node.js (Express)
    - Django
    - ReactJS
    - Bootstrap
- **Database**
    - Mysql
    - Oracle
    
# ***Project, Works, Honors And other experiences***
- แข่งขัน TOT Hackathon 2019 (รองชนะเลิศ)
- โปรเจ็คจบ Smart parking(IOT)
- Project ***WORLD MONEY***

### Node.js
```JavaScript
// สมัครสมาชิก
exports.signup = (req, res) => {
    const { email, password, activateToRegister = false } = req.body
    User.create({
            email: email,
            password: bcrypt.hashSync(password, 8),
            activateToRegister: activateToRegister
        })
        .then(user => {
            if (req.body.roles) {
                Role.findAll({
                    where: {
                        name: {
                            [Op.or]: req.body.roles
                        }
                    }
                }).then(roles => {
                    user.setRoles(roles).then(() => {
                        res.send({ message: "User was registered successfully!" });
                        console.info(`${req.method} ${req.originalUrl}`);
                    });
                });
            } else {
                // set user role = 1
                user.setRoles([1]).then(() => {
                    Token.create({
                            userId: user.id,
                            token: cryptoRandomString({ length: 16, type: 'hex' });
                        })
                        .then(token => {
                            sendVerificationEmail(user, token, req, res);
                        });
                    res.send({ message: "User was registered successfully!" });
                });
                console.info(`${req.method} ${req.originalUrl}`);
            }
        })
        .catch(err => {
            res.status(500).send({ message: err.message });
        });
};

// เข้าสู่ระบบ
exports.signin = (req, res) => {
    User.findOne({
            where: { email: req.body.email }
        })
        .then(user => {
            if (!user) {
                return res.status(404).send({ message: "User Not found." });
            }
            var passwordIsValid = bcrypt.compareSync(
                req.body.password,
                user.password
            );
            if (!passwordIsValid) {
                return res.status(401).send({ accessToken: null, message: "Invalid Password!" });
            }
            if (user.activateToRegister === false) {
                return res.status(401).send({ accessToken: null, message: "Please verify account!" });
            }
            var token = jwt.sign({ id: user.id }, config.secret, {
                expiresIn: 86400
            });

            var authorities = [];
            user.getRoles().then(roles => {
                for (let i = 0; i < roles.length; i++) {
                    authorities.push("ROLE_" + roles[i].name.toUpperCase());
                }
                res.status(200).send({
                    id: user.id,
                    email: user.email,
                    roles: authorities,
                    firstName: user.firstName,
                    lastName: user.lastName,
                    tel: user.tel,
                    status: user.status,
                    education: user.education,
                    activateToRegister: user.activateToRegister,
                    accessToken: token
                });
                sendLoginEmail(user, req, res)
                console.info(`${req.method} ${req.originalUrl} ${user.email}`);
            });
        })
        .catch(err => {
            res.status(500).send({ message: err.message });
        });
};
```



