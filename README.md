# Node-Classification-in-Website-Fingerprinting
It aims to identify the specific webpages in encrypted traffic by observing patterns of traffic traces.
 It is compulsory to have Tor. Step to install Tor:


``` bash
# For Debian or Ubuntu
sudo apt install tor lynx

# For Fedora
sudo yum install tor lynx

# For ArchLinux
sudo pacman -S tor torsocks lynx
```

Then run this:

``` bash
# SSH through Tor.
torsocks ssh user@example.com

# CUrl through Tor.
torsocks curl -L http://httpbin.org/ip

```


Activate a virtual environment by:

``` bash
cd path/to/website-fingerprinting
python -m venv $PWD/venv
source venv/bin/activate
```

Then install all the dependencies:

``` bash
pip install -r requirements.txt
```

## Data Collection

For the data collection process two terminal windows in a side-by-side orientation are required, as this process is fairly manual. Also, it's advised to collect the fingerprints in a VM, in order to avoid caputring any unintended traffic. To listen on traffic there exists a script, namely [capture.sh](pcaps/capture.sh), which should be run in one of the terminals:

``` bash
./pcaps/capture.sh duckduckgo.com
```

Once the listener is capturing traffic, on the next terminal run:

``` bash
torsocks lynx https://duckduckgo.com
```

Once the website has finished loading, the capture process needs to be killed, along with the browser session (by hitting the `q` key twice). The process should be repeated several times for each web page so that there is enough data.

## Machine Learning

[Scikit Learn](http://scikit-learn.org/stable/) was used to write a [k Nearest Neighbors](http://scikit-learn.org/stable/modules/neighbors.html#nearest-neighbors-classification) classifier, that would read the pcap files, as specified in the [config.json](config.json) file. `config.json` can be changed according to which webpages were targeted for training. The training script is [gather_and_train.py](gather_and_train.py).

<p align="center">
    <img src="https://scikit-learn.org/stable/_images/sphx_glr_plot_classification_001.png" alt="Scikit Learn kNN" />
</p>

## Classifying Unknown Traffic

```bash
# python predict.py [packet to classify]
  python predict.py xyz.pcap
```
Once the training is done, and the `classifier-nb.dmp` is created, the [predict.py](predict.py) script can be run with the pcap file as the sole argument. The script will load the classifier and attempt to identify which web page the traffic originated from.

It is worth noting that from each sample only the first 40 packets will be used to train a usable model and to run through the resulting classifier.


