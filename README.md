cryptonote-shang-pool
======================

High performance Node.js (with native C addons) mining pool for Cryptonote based coins, optimized for Shangcoin, with the support of Forknote software such as Bytecoin, etc..

Comes with lightweight example front-end script which uses the pool's AJAX API.
 
 

### Basic features

* TCP (stratum-like) protocol for server-push based jobs
  * Compared to old HTTP protocol, this has a higher hash rate, lower network/CPU server load, lower orphan
    block percent, and less error prone
* IP banning to prevent low-diff share attacks
* Socket flooding detection
* Payment processing
  * Splintered transactions to deal with max transaction size
  * Minimum payment threshold before balance will be paid out
  * Minimum denomination for truncating payment amount precision to reduce size/complexity of block transactions
* Detailed logging
* Ability to configure multiple ports - each with their own difficulty
* Variable difficulty / share limiter
* Share trust algorithm to reduce share validation hashing CPU load
* Clustering for vertical scaling
* Modular components for horizontal scaling (pool server, database, stats/API, payment processing, front-end)
* Live stats API (using AJAX long polling with CORS)
  * Currency network/block difficulty
  * Current block height
  * Network hashrate
  * Pool hashrate
  * Each miners' individual stats (hashrate, shares submitted, pending balance, total paid, etc)
  * Blocks found (pending, confirmed, and orphaned)
* An easily extendable, responsive, light-weight front-end using API to display data
 


Usage
=====

### Requirements
* Coin daemon(s) (find the coin's repo and build latest version from source)
* Coin RPC wallet(s) 
* simplewallet for Forknote based cryptocurrency (Bytecoin/Forknote v1.1.11).  Do NOT use Forknote 2.0.0, since it's in alpha
* [Node.js](http://nodejs.org/) v0.10+ ([follow these installation instructions](https://github.com/joyent/node/wiki/Installing-Node.js-via-package-manager))
* [Redis](http://redis.io/) key-value store v2.6+ ([follow these instructions](http://redis.io/topics/quickstart))
* libssl required for the node-multi-hashing module. For Ubuntu: `sudo apt-get install libssl-dev`


#### Seriously
Those are legitimate requirements. If you use old versions of Node.js or Redis that may come with your system package manager then you will have problems. Follow the linked instructions to get the last stable versions.
**Note**: *Version 0.10.^ of Node.js is most suitable with the actual development stage of the project*.


[**Redis security warning**](http://redis.io/topics/security): be sure firewall access to redis - an easy way is to
include `bind 127.0.0.1` in your `redis.conf` file. Also it's a good idea to learn about and understand software that
you are using - a good place to start with redis is [data persistence](http://redis.io/topics/persistence).

#### Installation
Installing pool on different Linux distributives is different because it depends on system default components and versions.

##### On Ubuntu 14 LTS
For now the easiest way to install pool is to use Ubuntu 14 LTS. Thus, all you had to do in order to prepare Ubuntu 14 for pool installation is to run:

```bash
sudo apt-get install git build-essential redis-server libboost1.55-all-dev nodejs-dev nodejs-legacy npm cmake libssl-dev
```

##### On Ubuntu 16 LTS

```bash
sudo apt-get install git build-essential redis-server libboost-all-dev nodejs-dev nodejs-legacy npm cmake libssl-dev
```

Version 0.10.^ of Node.js is most suitable with the actual development stage.
That said, since Ubuntu 16 come with version 4.^ of Node.js you will need to downgrade it (just to run the pool).

```bash
# install n to manage node version to use
sudo npm install -g n
# use node's version 0.10.^
sudo n 0.10
# you can change after to lastest node's LTS by running `sudo n lts`
# learn more with `n --help`
```

#### 1) Downloading & Installing


Clone the repository and run `npm update` for all the dependencies to be installed:

```bash
git clone https://github.com/shangproject/shang-pool.git    
cd shang-pool
npm update
```
 

#### 2) Start the pool
> These instructions, assuming your running Node.js version 0.10.^
```bash
node init.js
```
 
#### 3) Host the front-end

```bash
sudo apt-get install apache2
```

Simply host the contents of the html directory on file server capable of serving simple static files.
/var/www
  
 

#### Upgrading
When updating to the latest code its important to not only `git pull` the latest from this repo, but to also update
the Node.js modules, and any config files that may have been changed.
* Inside your pool directory (where the init.js script is) do `git pull` to get the latest code.
* Remove the dependencies by deleting the `node_modules` directory with `rm -rf node_modules`.
* Run `npm update` to force updating/reinstalling of the dependencies.
* Compare your `config.json` to the latest example ones in this repo or the ones in the setup instructions where each config field is explained. You may need to modify or add any new changes.

### Setting up Testnet

No cryptonote based coins have a testnet mode (yet) but you can effectively create a testnet with the following steps:

* Open `/src/p2p/net_node.inl` and remove lines with `ADD_HARDCODED_SEED_NODE` to prevent it from connecting to mainnet (Monero example: http://git.io/0a12_Q)
* Build the coin from source
* You now need to run two instance of the daemon and connect them to each other (without a connection to another instance the daemon will not accept RPC requests)
  * Run first instance with `./forknoted --p2p-bind-port 28080 --allow-local-ip`
  * Run second instance with `./forknoted --p2p-bind-port 5011 --rpc-bind-port 5010 --add-peer 0.0.0.0:28080 --allow-local-ip`
* You should now have a local testnet setup. The ports can be changes as long as the second instance is pointed to the first instance, obviously

*Credit to surfer43 for these instructions*


### JSON-RPC Commands from CLI

Documentation for JSON-RPC commands can be found here:
* Daemon https://wiki.bytecoin.org/wiki/Daemon_JSON_RPC_API
* Wallet https://wiki.bytecoin.org/wiki/Bytecoin_RPC_Wallet_API


Curl can be used to use the JSON-RPC commands from command-line. Here is an example of calling `getblockheaderbyheight` for block 100:

```bash
curl 127.0.0.1:18081/json_rpc -d '{"method":"getblockheaderbyheight","params":{"height":100}}'
```


### Monitoring Your Pool

* To inspect and make changes to redis I suggest using [redis-commander](https://github.com/joeferner/redis-commander)
* To monitor server load for CPU, Network, IO, etc - I suggest using [New Relic](http://newrelic.com/)
* To keep your pool node script running in background, logging to file, and automatically restarting if it crashes - I suggest using [forever](https://github.com/nodejitsu/forever)




### Community / Support

* [CryptoNote Technology](https://cryptonote.org)
* [CryptoNote Forum](https://forum.cryptonote.org/)
* [CryptoNote Universal Pool Forum](https://bitcointalk.org/index.php?topic=705509)
* [Forknote](https://forknote.net)
 


Credits
===

* [LucasJones](//github.com/LucasJones)  
* [surfer43](//github.com/iamasupernova)  
* [wallet42](http://moneropool.com) 
* [Wolf0](https://bitcointalk.org/index.php?action=profile;u=80740) 
* [Tacotime](https://bitcointalk.org/index.php?action=profile;u=19270) s
* [fancoder](https://github.com/fancoder/)  

License
-------
Released under the GNU General Public License v2

http://www.gnu.org/licenses/gpl-2.0.html
