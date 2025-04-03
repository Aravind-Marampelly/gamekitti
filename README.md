class SoundGame extends Phaser.Scene {
    constructor() {
        super({ key: 'SoundGame' });
    }

    preload() {
        this.load.image('lion', 'https://upload.wikimedia.org/wikipedia/commons/7/73/Lion_waiting_in_Namibia.jpg');
        this.load.image('car', 'https://upload.wikimedia.org/wikipedia/commons/3/3d/Lamborghini_Aventador_LP700-4.jpg');
        this.load.image('bird', 'https://upload.wikimedia.org/wikipedia/commons/e/ec/House_sparrow_male_in_India.jpg');
        this.load.image('house', 'https://upload.wikimedia.org/wikipedia/commons/6/60/House_Flat_Roof.jpg');
        this.load.image('tree', 'https://upload.wikimedia.org/wikipedia/commons/6/6d/Oak_tree.jpg');
        this.load.image('city', 'https://upload.wikimedia.org/wikipedia/commons/3/3f/NYC_Midtown_Skyline_at_night_-_Jan_2006_edit1.jpg');
        this.load.image('ball', 'https://upload.wikimedia.org/wikipedia/commons/3/3e/Soccerball.svg');
        this.load.image('background', 'https://upload.wikimedia.org/wikipedia/commons/d/dc/Soft_pastel_background.jpg');
        this.load.image('adPlaceholder', 'https://via.placeholder.com/300x100?text=Your+Ad+Here');
        
        this.load.audio('bgMusic', 'https://www.freesound.org/data/previews/250/250630_4486188-lq.mp3');
    }

    create() {
        this.add.image(400, 200, 'background').setScale(2);
        this.music = this.sound.add('bgMusic', { loop: true, volume: 0.5 });
        this.music.play();
        
        this.createMusicButton();
        this.createRewardedAdButton();
        this.loadCustomAd();
    }

    createMusicButton() {
        let musicButton = this.add.text(700, 20, 'Music: ON', { fontSize: '20px', fill: '#fff' })
            .setInteractive()
            .on('pointerdown', () => {
                if (this.music.isPlaying) {
                    this.music.stop();
                    musicButton.setText('Music: OFF');
                } else {
                    this.music.play();
                    musicButton.setText('Music: ON');
                }
            });
    }

    createRewardedAdButton() {
        let adButton = this.add.text(650, 50, 'Watch Ad', { fontSize: '20px', fill: '#fff', backgroundColor: '#000' })
            .setInteractive()
            .on('pointerdown', () => {
                this.showRewardedAd();
            });
    }

    showRewardedAd() {
        if (typeof admob !== 'undefined' && admob.rewarded) {
            admob.rewarded.show().then(() => {
                alert('Thanks for watching! You unlocked a bonus.');
            }).catch(() => {
                alert('Ad failed to load.');
            });
        } else {
            alert('Ads not available.');
        }
    }

    loadCustomAd() {
        fetch('https://yourserver.com/getAd') // Replace with your backend URL
            .then(response => response.json())
            .then(data => {
                let adImage = data.image || 'adPlaceholder';
                let adUrl = data.url || '#';
                let ad = this.add.image(400, 350, adImage).setInteractive();
                ad.setScale(0.5);
                ad.on('pointerdown', () => {
                    window.open(adUrl, '_blank');
                });
            })
            .catch(() => {
                console.log('Failed to load custom ad');
            });
    }
}

const config = {
    type: Phaser.AUTO,
    width: 800,
    height: 400,
    scene: SoundGame
};

const game = new Phaser.Game(config);

// Load AdMob script
document.write('<script src="https://pagead2.googlesyndication.com/pagead/js/adsbygoogle.js" async><\/script>');
