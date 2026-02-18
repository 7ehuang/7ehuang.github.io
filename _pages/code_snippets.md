---
layout: archive
title: "Code Snippets"
permalink: /code-snippets/
author_profile: true

---

{% include base_path %}

## Using variables with ggplot2
Sometimes you want to make a lot of plots to assess a list of variables. This is how you can pass variables to ggplot2 and plot all the plots in a list together with the `plot_grid` function from the `cowplot` package.

```{r}
library(ggplot2)
library(cowplot)
data(iris)
variables = setdiff(names(iris), 'Species')
plot_list = list()
for(var in variables){
  p = ggplot(iris, aes(x = Species, y = !!sym(var))) + geom_boxplot(aes(fill = Species), width = 0.4) + theme_classic() + theme(legend.position = 'none')
  plot_list[[var]] = p
}

plot_grid(plotlist = plot_list)
```

## Specifying variables in linear models 
Sometimes you want to test a lot of different linear models. This is how you can ask R to parse custom strings with `lm()`. 

```{r}
data(iris)
variables = c('Sepal.Length', 'Sepal.Width', 'Petal.Length')
res_list = list()
for(var in variables){
  model_expression = paste0('lm(Petal.Width~', var,  ',data = iris)') 
  print(model_expression)
  model_res = eval(parse(text= model_expression))
  res_list[[var]] = summary(model_res)$coef %>% as.data.frame() %>% mutate(var = var)
}

all_model_res = do.call("rbind", res_list)

```

## Combining rows with the same column value (paste rows together)
Specify the grouping variable second, and use the `collapse` parameter to specify how you want the previously separate rows delimited. 
```{r}
data(iris)
aggregate(data = iris, Petal.Width ~ Species, FUN = paste0, collapse = ',')

```
