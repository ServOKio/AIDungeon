# AIDungeon2

Read more about AIDungeon2 and how it was built [here](https://pcc.cs.byu.edu/2019/11/21/ai-dungeon-2-creating-infinitely-generated-text-adventures-with-deep-learning-language-models/).

Play the mobile app version of the game by following the links [here](https://aidungeon.io)

Play the game online by following this link [here](https://play.aidungeon.io)

Play the game in Colab [here](https://colab.research.google.com/github/AIDungeon/AIDungeon/blob/master/AIDungeon_2.ipynb).

## Installation and launch
> To play the game locally, it is recommended that you have an nVidia GPU with 12 GB or more of memory, and CUDA installed. If you do not have such a GPU, each turn can take a couple of minutes or more for the game to compose its response.

### Linux systems
```
git clone --branch master https://github.com/AIDungeon/AIDungeon/
cd AIDungeon
./install.sh # Installs system packages and creates python3 virtual environment
./download_model.sh
source ./venv/bin/activate
./play.py
```
### Windows

`1` Install dependencies (Python 3.8 apparently does not work)
You need
1. `Torrent client` - to download model. I use [qBittorrent](https://www.qbittorrent.org/)
2. `Python < v4` - ro run. I use [Python 3.7.9](https://www.python.org/ftp/python/3.7.9/python-3.7.9-amd64-webinstall.exe) ⚠ Warning ⚠ Do not download Python from the Microsoft App Store. This version does not see system paths.
3. and `Git` - to download this repository. Of course, you can just download the ZIP and unpack it to the place where your game will be located.

`2` Download the model via torrent
<br/>
To work, you must download the model. That's about +6GB

Open qBittorrent. Click the magnet icon in the top left.    
![image](https://user-images.githubusercontent.com/2887489/70790237-94f60700-1d8c-11ea-84ed-2cbe4dcb1686.png)    
Copy the magnet link here.
```
magnet:?xt=urn:btih:b343b83b35bff774dab13e0281ce13b3daf37d3e&dn=model_v5&tr=udp%3a%2f%2ftracker.coppersurfer.tk%3a6969%2fannounce&tr=udp%3a%2f%2ftracker.leechers-paradise.org%3a6969%2fannounce&tr=udp%3a%2f%2ftracker.opentrackr.org%3a1337%2fannounce&tr=udp%3a%2f%2ftracker.pomf.se%3a80%2fannounce&tr=udp%3a%2f%2ftracker.openbittorrent.com%3a80%2fannounce&tr=udp%3a%2f%2fp4p.arenabg.com%3a1337%2fannounce&tr=udp%3a%2f%2f9.rarbg.me%3a2710%2fannounce&tr=udp%3a%2f%2f9.rarbg.to%3a2710%2fannounce&tr=udp%3a%2f%2fexodus.desync.com%3a6969%2fannounce&tr=udp%3a%2f%2ftracker.tiny-vps.com%3a6969%2fannounce&tr=udp%3a%2f%2fopen.stealth.si%3a80%2fannounce&tr=udp%3a%2f%2fdenis.stalker.upeer.me%3a6969%2fannounce&tr=udp%3a%2f%2ftracker.torrent.eu.org%3a451%2fannounce&tr=udp%3a%2f%2ftracker.moeking.me%3a6969%2fannounce&tr=udp%3a%2f%2ftracker.cyberia.is%3a6969%2fannounce&tr=udp%3a%2f%2fopen.demonii.si%3a1337%2fannounce&tr=udp%3a%2f%2fipv4.tracker.harry.lu%3a80%2fannounce&tr=udp%3a%2f%2ftracker3.itzmx.com%3a6961%2fannounce&tr=udp%3a%2f%2fzephir.monocul.us%3a6969%2fannounce&tr=udp%3a%2f%2fxxxtor.com%3a2710%2fannounce
```    
Add the magnet link to the box as pictured below.    
<img src="https://user-images.githubusercontent.com/2887489/70790273-a63f1380-1d8c-11ea-99f4-dc8c2e3a50cb.png" width="500px"/>  
Save the model file accordingly, as pictured below.    
<img src="https://user-images.githubusercontent.com/2887489/70790694-84925c00-1d8d-11ea-9b28-b2c4a938e8cd.png" width="500px"/>
<br/>
(If the folder does not exist, create it)

`3` Run PowerShell as Administrator as pictured below.
<br/>
<img src="https://user-images.githubusercontent.com/2887489/70789938-e782f380-1d8b-11ea-837a-d624fa3be695.png" width="500px"/>

`4` Some commands


## Finetune the model yourself

Formatting the data. After scraping the data I formatted text adventures into a json dict structure that looked like the following:
```
{   
    "tree_id": <someid>
    "story_start": <start text of the story>
    "action_results": [
    {"action":<action1>, "result":<result1>, "action_results": <A Dict that looks like above action results>},
    {"action":<action2>, "result":<result2>, "action_results": <A Dict that looks like above action results>}]
}
```
Essentially it's a tree that captures all the action result nodes. 
Then I used [this](https://github.com/AIDungeon/AIDungeon/blob/develop/data/build_training_data.py) to transform that data into one giant txt file. The txt file looks something like:
```
<|startoftext|>
You are a survivor living in some place...
> You search for food
You search for food but are unable to find any
> Do another thing
You do another thing...
<|endoftext|>
(above repeated many times)
```

Then once you have that you can use the [finetuning script](https://github.com/AIDungeon/AIDungeon/blob/develop/generator/simple/finetune.py) to fine tune the model provided you have the hardware.

Fine tuning the largest GPT-2 model is difficult due to the immense hardware required. I no longer have access to the same hardware so there are two ways I would suggest doing it. I originally fine tuned the model on 8 32GB V100 GPUs (an Nvidia DGX1). This allowed me to use a batch size of 32 which I found to be helpful in improving quality. The only cloud resource I could find that matches those specs is an aws p3dn.24xlarge instance so you'd want to spin that up on EC2 and fine tune it there. (might have to also request higher limits). Another way you could do it is to use a sagemaker notebook (similar to a colab notebook) and select the p3.24xlarge instance type. This is equivalent to 8 16 GB V100 GPUs. Because each GPU has only 16GB memory you probably need to reduce the batch size to around 8.


Community
------------------------

AIDungeon is an open source project. Questions, discussion, and
contributions are welcome. Contributions can be anything from new
packages to bugfixes, documentation, or even new core features.

Resources:

* **Website**: [aidungeon.io](http://www.aidungeon.io/)
* **Email**: aidungeon.io@gmail.com
* **Twitter**: [creator @nickwalton00](https://twitter.com/nickwalton00), [dev @benjbay](https://twitter.com/benjbay)
* **Reddit**: [r/AIDungeon](https://www.reddit.com/r/AIDungeon/)
* **Discord**: [aidungeon discord](https://discord.gg/Dg8Vcz6)


Contributing
------------------------
Contributing to AIDungeon is easy! Just send us a
[pull request](https://help.github.com/articles/using-pull-requests/)
from your fork. Before you send it, summarize your change in the
[Unreleased] section of [the CHANGELOG](CHANGELOG.md) and make sure
``develop`` is the destination branch.

AIDungeon uses a rough approximation of the
[Git Flow](http://nvie.com/posts/a-successful-git-branching-model/)
branching model.  The ``develop`` branch contains the latest
contributions, and ``master`` is always tagged and points to the latest
stable release.

If you're a contributor, make sure you're testing and playing on `develop`.
That's where all the magic is happening (and where we hope bugs stop).
