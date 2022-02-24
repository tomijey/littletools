import toad
"""
rule_cob = toad.transform.Combiner()
rule_cob.fit(train_data[fet], train_data[target], n_bins=10, empty_separate=True)
train_data_bin = rule_cob.transform(train_data[fet], labels=True)
woe = toad.transform.WOETransformer()
train_data_woe = woe.fit_transform(train_data_bin, train_data[target])
scorecard = toad.scorecard.ScoreCard(combiner=rule_cob, transer=woe, max_iter=10000)
scorecard.fit(train_data_woe, train_data[target])
score_dict = scorecard.export()
"""

class Lr2Sql(object):
    def __init__(self, score):
        self.score_dict = score
        
    def feature2score(self, feature, score):
        sql = 'case '
        for k, s in score.items():
            sql += '\n\twhen '
            if k.startswith('['):
                k = k.strip('[').strip(')')
                left, right = k.split('~')
                if '-inf' in left:
                    sql += f'{feature} < {right} '
                elif 'inf' in right:
                    sql += f'{feature} >= {left} '
                else:
                    sql += f'{feature} >= {left} and {feature} < {right} '
            elif k == 'nan':
                sql += f'{feature} is null '  
            else:
                cond = '("' + '","'.join(k.split(',')) + '")'
                sql += f'{feature} in {cond} '
            sql += f' then {s} '
        sql += f'\n end as {feature}_score'
        return sql
    def lr2sql(self):
        lrsql = []
        for feature, score in self.score_dict.items():
            lrsql.append(self.feature2score(feature, score))
        return '\n,'.join(lrsql)
l2s = Lr2Sql(socre_dict)
runsql = l2s.lr2sql()       
