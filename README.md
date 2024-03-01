# Setup

To download the large `.r1cs`, `.ptau`, and `.zkey` files in this repo, [install git-lfs](https://git-lfs.com/). 

If needed, [install npm](https://docs.npmjs.com/downloading-and-installing-node-js-and-npm), and also [install nvm](https://github.com/nvm-sh/nvm?tab=readme-ov-file#installing-and-updating). Then run the commands

```
nvm use 18
```

and

```
npm install snarkjs@0.6.11
```

to install the version of `snarkjs` used by our code which was used to run the setup ceremony. 

To verify the hash values of various files presented in this document, ensure [Homebrew is installed](https://docs.brew.sh/Installation) and run 

```
brew install b2sum
```

The `b2sum` hash of a file may be computed by running `b2sum <filename>`.

# Verifying the Phase 1 Powers of Tau File

The powers of tau file used was downloaded from one of the links provided here in the [Snarkjs README](https://github.com/iden3/snarkjs/blob/master/README.md#7-prepare-phase-2), as the file titled `powersOfTau28_hez_final_21.ptau`. This `.ptau` file contains 54 contributions and a random beacon. Its `b2sum` hash is 

```
9aef0573cef4ded9c4a75f148709056bf989f80dad96876aadeb6f1c6d062391f07a394a9e756d16f7eb233198d5b69407cca44594c763ab4a5b67ae73254678
``` 

To verify this file, run 

```
npx snarkjs@0.6.11 powersoftau verify powersOfTau28_hez_final_21.ptau -v
```

# Reproducing the .r1cs file

To reproduce `main.r1cs`, clone the [Aptos Keyless Circuit Repo](https://github.com/aptos-labs/aptos-keyless-circuit), checkout commit `2a1d445a49d212fa55c322e6f3373631bc74140a`, and run the following commands:

```
cd templates
circom -l . main.circom --r1cs
```

The `b2sum` hash of the resulting `main.r1cs` file should be 

```
18d68f469a6ead62aafcc9c78fa1b99b4391b9a5acfca2be62722503684f98ba3d84a764d2aa9143898797bf33ab0e4c9fb2584e2afa660e0100cc8af7eb5226
```

which is identical to the `b2sum` hash of `main.r1cs` in this repo.

This provides a link between our circuit code and the trusted setup, ensuring you can verify the setup was done over the correct codebase. 

# Reproducing the initial .zkey file

To reproduce the initial `.zkey` file, run the command

```
npx snarkjs@0.6.11 groth16 setup main.r1cs ./powersOfTau28_hez_final_21.ptau initial.zkey -v
```

The `b2sum` hash of the resulting `.zkey` file should match that of `contributions/main_00000.zkey`. This hash value is

```
d10eb2e278167011a7a205bdf3888d7df1723c8079243a81156092459b9f7341597aeba719187d82d11bfa16b3cba0955260520477d3094c2ffc7dbc99d2f0fa
```



# Verifying Individual Contributions


The folder `contributions` contains all `.zkey` files output by the Aptos OIDB trusted setup. Each `.zkey` file corresponds to the contribution of one participant, in the order of their having participated, so that i.e. `main_00004.zkey` corresponds to the output of the contribution made by the 4-th participant to have completed their contribution.

Each contribution may be verified by running the command 

```
npx snarkjs@0.6.11 zkey verify main.r1cs powersOfTau28_hez_final_21.ptau contributions/<contribution_filename>.zkey -v
```

Upon completion, this will produce an output of the following form: 

```
[INFO]  snarkJS: Circuit Hash: 
		0a9aeb1f 247e8652 46a00128 ab2fbe10
		86ad8f2a 86a17f72 bd35f095 dfde1a86
		646f6faa fdaa46d6 9dde1ad5 88f21d4d
		0d4e9e74 519b561d 9e71e768 9c739b68
[INFO]  snarkJS: -------------------------
[INFO]  snarkJS: contribution #3 sherry-x-55457892:
		0125c8cb de936779 25ff1b62 49eff731
		15a68679 899678bb 83a19c0e cf8daf28
		20dcf4a9 78192394 2895cbc0 1ba661b3
		1628f395 f553b409 16428355 cfd53f6b
[INFO]  snarkJS: -------------------------
[INFO]  snarkJS: contribution #2 michaelaptos-158355435:
		63c5d60f 668bd588 d6c86d0e a459f24d
		d75c207a 76dbc07a 36c466c5 97ef3ba7
		760e6dd3 b3162767 313935eb afe8fd59
		1b4868b0 238ee70c beb60e3b a46a38d6
[INFO]  snarkJS: -------------------------
[INFO]  snarkJS: contribution #1 alinush-1724810:
		65c3e44a e8ae9dcd 2406d1b4 7413e09c
		72a6587b 5dbc6e00 ac607c84 cefbc4c1
		a9dd4d2c 39d78381 ea2a8cf2 8d3f4e91
		c32c2e8b eaf17465 b9bc8045 a529ecf1
[INFO]  snarkJS: -------------------------
[INFO]  snarkJS: ZKey Ok!
```

This is a list of all contribution hashes up to and including the current contribution that you have just verified. If you participated in the setup, you can verify that your contribution is being used correctly by running this command on the final `.zkey` file, `contributions/main_final.zkey`, and comparing the output contribution hash corresponding to your contribution to the one which was printed out when you finished contributing during the ceremony. 

Note that the contribution hash is not simply a hash of the `.zkey` file, but a hash of the participant-specific parameters stored by every `.zkey` file after a participant's contribution has been made. Verification on the `i`-th `.zkey` file includes checks on these parameters for participants `1` through `i`. The output of this process is used in verifying the `.zkey` file overall when running the above `zkey verify` command. 

# Applying the randomness beacon

To eliminate potential bias, we apply the [Drand randomness beacon](https://drand.love/) to the final `.zkey` file to obtain our final `.zkey` file for use in production. To regenerate our randomness, first [follow the instructions here](https://drand.love/developer/drand-client/#installation) and then run

```
./drand-client --round 3793809 \
--chain-hash 8990e7a9aaed2ffed73dbd7092123d6f289930540d7651336225dc172e51b2ce \
--url http://api.drand.sh \
--relay /dnsaddr/api.drand.sh
3793809	da44fdb1c88a25fd68d8581e077dd9e4d6d4c8af22c30b127a23dd8343995565
```

which should output round number `3793809` and randomness

```
da44fdb1c88a25fd68d8581e077dd9e4d6d4c8af22c30b127a23dd8343995565
```

You can recreate the final `.zkey` file by running

```
npx snarkjs@0.6.11 zkey beacon contributions/main_00139.zkey main_final.zkey da44fdb1c88a25fd68d8581e077dd9e4d6d4c8af22c30b127a23dd8343995565 10 -v
```

This should output

```
[INFO]  snarkJS: Contribution Hash: 
		58b4bcad 0b9c62cd 1cbd1671 ac267e9c
		18d943f4 2e6a2992 d67e571f 4738969c
		48f73ad3 d2e01828 6f395a33 48038b9d
		eff3e750 dd2dfcff 82bf8da5 2b7b844d
```

As with other contributions, this `.zkey` file may be verified by running

```
npx snarkjs@0.6.11 zkey verify main.r1cs powersOfTau28_hez_final_21.ptau main_final.zkey -v
```

The `b2sum` hash of the resulting `main_final.zkey` should be 

```
d10eb2e278167011a7a205bdf3888d7df1723c8079243a81156092459b9f7341597aeba719187d82d11bfa16b3cba0955260520477d3094c2ffc7dbc99d2f0fa
```

which is identical to the `b2sum` hash of `contributions/main_final.zkey`.

# Reproducing the verification key

The final verification key can be exported using 

```
npx snarkjs@0.6.11 zkey export verificationkey contributions/main_final.zkey verification_key_regen.vkey
```

Its `b2sum` hash is

```
c875deca3fcc691279d09acf34a83c5400d23d366ca7d310c7f3ca57fc2ca0e6d9f55bf417b5f8ee430b91b07552b843d450b3818cca16088f58cdc07669b975
```

# Participant List

The list of participants who both completed their contribution and consented to have their identity listed are presented below. We would like to sincerely thank both them and those who have chosen to remain anonymous for contributing to the security of Aptos Keyless. 

```
Hudson Jang
Silviu Gae
Daniel Porteous
winlin
Anne B
David Wolinsky
Brian Li
Tim Ch
Olrise
Max Kaplan
Greg Nazario
Prasanna Gautam
Rustie Lin
Keyrock
Edouard Lavidalle
David Conroy
Robert Chen
Geoff Tang
Brian Murphy
Saanika
Richard Zhang
Marcus | Elagabalx
Kirill
Anna Maria
Vineeth Kashyap
spichloner
HWANGJAE LEE
Sherry Xiao
Maayan
Marco Ilardi
Xianyun Li
Anna
Christopher Dowle
Aaron Lint
Christian Theilemann
Taras
William White
KappaRoss
Ye Park
Brian Boehlke
Zekun Li
Pavel Zykin
Hyung-Kyu Choi (DSRV)
Vladislav Anikeev
Yuun Lim
Pacobits | Stakely
Victor Gao
Alyssa Ponzo
Artsiom Holikau
Aleksandre Khokhiashvili
Aaron Gao
Blake Zimmerman
Philip Vu
Dmytro
Darren Park
Schultzie
Slava
Bowen Yang
Greg North
Ahri
Anto
Gabriele Della Casa Venturelli
hyerim kim
change
Mason Hall
Sasha Spiegelman
Davide
Kshitij Chakravarty
John Youngseok Yang
Evgeny Garanin
Alin Tomescu
mason money
Avery
antotg
Andrea Cappa
Shachindra
Renee Tso
guguru
Andrei
Benny Pinkas
Teng Zhang
Junha Park
Jumanzi Han
Young Yan Liauw
Josh Lind
Zekun Wang
Perry Randall
Junkil Park
Jad
Polina Voloshina
Lazy Notre
Mark Ellis
Jill Xu
Kent White
Maksim Voloshin
Larry Liu
Zhuolun Xiang
CryptoJack
moran666666
Rex Fernando
Michael Straka
Aleks
Darron Park
Phil
Suryansh
```
