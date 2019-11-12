# Singularity Game
The game runs complete on blockchain. See https://singularitygameforall.github.io/
# Rule summary
* An account can only accept one investment before exiting, and can continue to invest after exiting
* Static dividends fluctuate by 0.4%~1.5% per day
* Will drop out of the game after having earned 3 times of investment with 1-5 ETH; 4 times of investement with 6-15 ETH; 5 times of investment with 15-45 ETH; 6 times of investment with 46 ETH and above.
* The referer will receive 9% of the referee's investment. And the grandparent referer will receive 6%. The money will arrive instantly.
* Individual awards (small prizes) every 24 hours; three lucky winners every week.
* Every person has an opportunity to win 1 or more times a week. Per person per month, the bonus accounts for 1% to 5% of the activated account investiment amount.
* After becoming a professional player, you are eligible to share the winning prizes of all direct/indirect referees under you.
# Source code analysis
    // 3% for shareholders
    function distShareHolders(Data storage _data) internal {
        uint256 val = msg.value.mul(3).div(100);
        uint len = _data.shareHolders.length;
        for (uint i = 0; i < len; i++) {
            _data.shareHolders[i].transfer(val.div(len));
        }
    }

    // 5% for developers
    function distDevelopers(Data storage _data) internal {
        uint256 val = msg.value.mul(5).div(100);
        uint len = _data.developers.length;
        for (uint i = 0; i < len; i++) {
            uint256 value = val.mul(_data.developerWeights[i]).div(100);
            _data.developers[i].transfer(value);
        }
    }

    function distGlobalShareHolders(Data storage _data) internal {
        uint256 val = msg.value.mul(9).div(100);
        uint256 sum = 0;
        uint len = _data.globalShareHolders.length;
        address receiver;
        for (uint i = 0; i < len; i++) {
            receiver = _data.globalShareHolders[i];
            if(_data.regM[receiver]) {
                sum = sum.add(_data.plyr[receiver].meth);
            }
        }
        for (i = 0; i < len; i++) {
            if(_data.regM[_data.globalShareHolders[i]]) {
                receiver = _data.globalShareHolders[i];
                uint256 sendValue = val.mul(_data.plyr[receiver].meth).div(sum);
                _data.plyr[receiver].share = _data.plyr[receiver].share.add(sendValue);
            }
        }
    }

    // 9% for the first layer and 6% for the second.
    function distDy(Data storage _data) internal {
        address rec1 = _data.recommandLinkM[msg.sender];
        if(rec1 != 0) {
            uint256 dyVal = msg.value.mul(9).div(100);
            _data.plyr[rec1].dy = _data.plyr[rec1].dy.add(dyVal);
            _data.plyr[rec1].tdy = _data.plyr[rec1].tdy.add(dyVal);
            address rec2 = _data.recommandLinkM[rec1];
            if(rec2 != 0) {
                dyVal = msg.value.mul(6).div(100);
                _data.plyr[rec2].dy = _data.plyr[rec2].dy.add(dyVal);
                _data.plyr[rec2].tdy = _data.plyr[rec2].tdy.add(dyVal);
            }
        }
    }
