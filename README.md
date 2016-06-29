
-------- Comparison of lme4, rstanarm, and glmmTMB with random effects in R -----------

-------- Model with Days|Subject given below shows stan_glmer() gives slight differences in random effects  -------------


    require(rstanarm)
    require(glmmTMB)
    require(lme4)

    data(sleepstudy, package="lme4")

    summary(TMB.fm1 <- glmmTMB(Reaction ~ Days + (1|Subject), sleepstudy))
    summary(lme4.fm1 <- lmer(Reaction ~ Days + (1|Subject), sleepstudy))
    summary(Stan.fm1 <- stan_glmer(Reaction ~ Days + (1|Subject), sleepstudy, chains = 4, cores = 4, iter = 2000))


 ------- Fixed effects comparison with Std. Errors -----------

    summary(TMB.fm1)$coefficients$cond
    summary(lme4.fm1)$coefficients
    data.frame(Estimate=fixef(Stan.fm1), Std.Error = se(Stan.fm1)[1:2])


 ------- Random effects comparison with Std. Errors -----------

    VarCorr(TMB.fm1)
    VarCorr(Stan.fm1)
    VarCorr(lme4.fm1)


    (RndmEffct <- cbind(ranef(TMB.fm1)$cond$Subject, ranef(Stan.fm1)$Subject, ranef(lme4.fm1)$Subject))
    matplot(rownames(RndmEffct), RndmEffct)


 
 ------- Graphical posterior predictive checks for the Stan model -----------


    pp_check(Stan.fm1, "resid", nreps=30)
    pp_check(Stan.fm1, "resid", nreps=30)
    pp_check(Stan.fm1, "scat", nreps=30)
    pp_check(Stan.fm1, "test")



 ------- Faster, but less accurate algorithms for the Stan model -----------

    summary(Stan.fm2 <- stan_glmer(Reaction ~ Days + (1|Subject), sleepstudy, algorithm="meanfield"))
    summary(Stan.fm3 <- stan_glmer(Reaction ~ Days + (1|Subject), sleepstudy, algorithm="fullrank"))


    data.frame(Estimate=fixef(Stan.fm1), Std.Error = se(Stan.fm1)[1:2])
    data.frame(Estimate=fixef(Stan.fm2), Std.Error = se(Stan.fm2)[1:2])
    data.frame(Estimate=fixef(Stan.fm3), Std.Error = se(Stan.fm3)[1:2])


 ------------------- Model with Days|Subject ----------------------------------------------


    summary(TMB.fm4 <- glmmTMB(Reaction ~ Days + (Days|Subject), sleepstudy))
    summary(lme4.fm4 <- lmer(Reaction ~ Days + (Days|Subject), sleepstudy))
    summary(Stan.fm4 <- stan_glmer(Reaction ~ Days + (Days|Subject), sleepstudy, chains = 4, cores = 4, iter = 4000)) # Iterations up to 4,000


# ------- Fixed effects comparison with Std. Errors -----------

    summary(TMB.fm4)$coefficients$cond
    summary(lme4.fm4)$coefficients
    data.frame(Estimate=fixef(Stan.fm4), Std.Error = se(Stan.fm4)[1:2])


# ------- Random effects comparison with Std. Errors -----------

    VarCorr(TMB.fm4)
    VarCorr(Stan.fm4)
    VarCorr(lme4.fm4)


    (RndmEffct <- cbind(ranef(TMB.fm4)$cond$Subject, ranef(Stan.fm4)$Subject, ranef(lme4.fm4)$Subject))
    matplot(rownames(RndmEffct), RndmEffct[,c(1,3,5)]) # Intercept
    windows()
    matplot(rownames(RndmEffct), RndmEffct[,c(2,4,6)]) # Days










