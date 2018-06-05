## TX2のキャリアボードへの載せ替え

TX2標準の評価ボードだとフットプリントが大きすぎるため、サードパーティ製キャリアボードへの載せ替えを行う

- キャリアボードについて
  - Orbitty Carrier for NVIDIA® Jetson™ TX2 & Jetson™ TX1
  - http://connecttech.com/product/orbitty-carrier-for-nvidia-jetson-tx2-tx1/

![Orbitty](http://connecttech.com/wp-content/uploads/images/product-images/ASG003/ASG003_Orbitty-AngleA-600x433.jpg)

- USBを有効化

  TX2は回路保護のため？カスタムボードでUSBが無効になっているのでブートローダを書き換えてUSBを有効にする
  - [参考ページ](http://connecttech.com/resource-center/cti-l4t-nvidia-jetson-board-support-package-release-notes/)
  - 以下をホストPCへダウンロード
    - [JetPack 3.1R28.1](https://drive.google.com/open?id=1DdvjVXYouBSXU0CQmPY_ofy2s5rMAxeF)
    - [CTI-L4T-V111.tgz](https://drive.google.com/open?id=19nGud4UmyTPUO3bxwb_5zWrlwcsevgOG)
  - JetPack3.1を使って普通にTX2の初期化を行う
    ![jetpack_flash_os](https://user-images.githubusercontent.com/1901008/37550505-be1e17a4-29d1-11e8-8cfb-e126746fd091.png)
  - TX2のUbuntuの起動まで確認したら、TX2を再びRecovery modeにする
  - JetPackのインストールディレクトリへ移動する
  - TX2のブートローダへパッチを適用する

    ```bash
    $ cp CTI-L4T-V111.tgz <install_dir>/64_TX2/Linux_for_Tegra_tx2/
    $ cd <install_dir>/64_TX2/Linux_for_Tegra_tx2/
    $ tar zxf CTI-L4T-V111.tgz
    $ cd CTI-L4T
    $ sudo ./install.sh
    $ cd ..
    $ sudo ./flash.sh orbitty mmcblk0p1
    ```

    - TX2を再び通常起動して、USB接続ができることを確認する

- CUDA & OpenCVのインストール
  - TX2を通常起動した状態でJetPackを再度起動する
  ![jetpack_cuda](https://user-images.githubusercontent.com/1901008/37550577-ee846820-29d2-11e8-9eef-380f7a8b4019.png)
  - TX2のデフォルト環境設定にCUDA用の設定を追加

    ```bash
    # TX2で実行
    $ echo 'export PATH=/usr/local/cuda/bin:$PATH' > /etc/profile.d/cuda.sh
    $ echo 'export LD_LIBRARY_PATH=/usr/local/cuda/lib64:$LD_LIBRARY_PATH' >> /etc/profile.d/cuda.sh
    ```
