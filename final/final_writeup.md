# Malware Detection with Machine Learning: netML Leaderboard Reproduction

**Team:** Rhea Madhogarhia, Rohan Madhogarhia, Anushka Agrawal

## Goals

We trained machine learning models to detect malware in network traffic using the netML dataset, by classifying traffic as benign or malware. The goal was to see if we could match or beat the leaderboard results and figure out what makes malware packets different from normal ones.
Catching malware in network traffic is critical for security. In our malware detection, we focused on recall (catching as much malware as possible) because missing a real attack is worse than flagging something benign for review. In other words, a false positive is less harmful than a false negative in this scenario. 

## Approach

**Data Processing:** We analyzed packet captures and extracted network features like IP flags, packet lengths, TTL values, port numbers, and payload characteristics. We grouped packets by sample ID to prevent data leakage between training and test sets, a part of the training process that was stress during lectures by Prof Feamster.

**Models Tested:**
- Random Forest
- XGBoost  
- CatBoost

All models were tuned to handle class imbalance.

## Results

| Model | Accuracy | Malware Recall |
|-------|----------|----------------|
| **Random Forest** | 0.98 | 0.986 |
| XGBoost | 0.93 | 0.94 |
| CatBoost | 0.91 | 0.99 |

**Random Forest** won overall with the best balance. It caught nearly all malware (98.6%) while keeping false alarms low (our goal!). **CatBoost** had the highest malware recall (99%) but flagged **too many** benign packets as malware. **XGBoost** fell somewhere in the middle.

## Feature Importance

Feature importance analysis showed that models primarily relied on:
- **IP flags** (especially for XGBoost)
- **Packet lengths** and payload sizes
- **TTL values**
- **Checksum patterns**

Interestingly, port numbers mattered less than we expected. The models focused more on the structure of packets and so we are guessing that malware may have distinct packet shapes and timing patterns that stand out to the model (or at least these are the underlying complexities captured by the model)

## Results and Evaluation

Our results look slightly **too** good, and there are a few possible explanations:

**Dataset isn't realistic:** Malware made up about half our data, but in real networks malware is probably extremely rare. This makes our metrics look better than they'd be in production where false positives would be a much bigger problem.

**Possible overfitting concerns:** Our performance seems to exceed the netML leaderboard, which is suspicious. This could mean differences in preprocessing, feature engineering, or data splits. We'd need to investigate further to confirm our model is learning generalizable patterns but after making sure to get rid of any highly weighted features and to get rid of potential leakage between the test and train datasets, we still got above 98% accuracy. 

**Group splitting helped but isn't perfect:** As mentioned above, we prevented direct packet leakage by keeping samples together, but similar malware families or patterns could still appear in both train and test sets which may also contribute to our overfitting concerns.

## Future Directions
- Test on truly imbalanced data (like 99% benign traffic)
- Validate against completely unseen malware families
- Tune decision thresholds for different deployment scenarios
- Compare our exact setup with the original netML methodology
- Try deeper feature engineering or adding flow-level aggregations

## Conclusions

Random Forest is our winner because it's robust, doesn't overfit to single features, and delivers strong performance across both classes. With more tuning and realistic testing, this could be a solid foundation for real-world malware detection. Further analysis is required to understand why our predictive performance was so high and to confirm that our model is learning meaningful patterns.