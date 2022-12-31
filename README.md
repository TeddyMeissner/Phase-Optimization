# Phase-Optimization
{
 "cells": [
  {
   "cell_type": "code",
   "execution_count": 1,
   "id": "fc1382f9",
   "metadata": {},
   "outputs": [],
   "source": [
    "import numpy as np\n",
    "import matplotlib.pyplot as plt\n",
    "from scipy.optimize import minimize\n",
    "%matplotlib inline"
   ]
  },
  {
   "cell_type": "markdown",
   "id": "6f5be94d",
   "metadata": {},
   "source": [
    "We want to minimize the function, \n",
    "$$g = z^\\alpha g^\\alpha(x^\\alpha) + z^\\beta g^\\beta(x^\\beta) + Az^\\alpha z^\\beta$$\n",
    "Subject to: \n",
    "$$z^\\alpha + z^\\beta = 1$$\n",
    "$$z^\\alpha x^\\alpha + z^\\beta x^\\beta = X$$\n",
    "$$0 \\leq z^\\alpha, z^\\beta, x^\\alpha , x^\\beta, X \\leq 1$$\n",
    "\n",
    "Under the assumption that the free energies can be described by parabolic functions we examine the form, \n",
    "$$g^\\alpha(x^\\alpha) = a_\\alpha (x^\\alpha - x_0^\\alpha)^2 + b_\\alpha$$\n",
    "$$g^\\beta(x^\\beta) = a_\\beta (x^\\beta - x_0^\\beta)^2 + b_\\beta$$\n",
    "\n",
    "Where, $a_\\alpha, a_\\beta, b_\\alpha,b_\\beta, x_0^\\alpha, x_0^\\beta$ are known. "
   ]
  },
  {
   "cell_type": "markdown",
   "id": "34c9cabf",
   "metadata": {},
   "source": [
    "By substituting the equality constraints, we look to minimize, \n",
    "$$g = z^\\alpha g^\\alpha(x^\\alpha) + (1- z^\\alpha) g^\\beta(x^\\beta) + Az^\\alpha (1 - z^\\alpha)$$\n",
    "Subject to: \n",
    "$$z^\\alpha = \\frac{X - x^\\beta}{x^\\alpha - x^\\beta}$$\n",
    "$$0 \\leq z^\\alpha, z^\\beta, x^\\alpha , x^\\beta, X \\leq 1$$\n"
   ]
  },
  {
   "cell_type": "markdown",
   "id": "1d4937fe",
   "metadata": {},
   "source": [
    "$$\\textbf{Numerical}$$"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 2,
   "id": "fe47e915",
   "metadata": {},
   "outputs": [],
   "source": [
    "def two_phase_optimization(x_α_0,x_β_0,a_α,a_β,b_α,b_β,A,X):\n",
    "    def objective(sol):\n",
    "        x_α,x_β,z_α = sol[0],sol[1],sol[2]\n",
    "        g_α = a_α*(x_α-x_α_0)**2 + b_α\n",
    "        g_β = a_β*(x_β-x_β_0)**2 + b_β\n",
    "        return z_α*g_α + (1-z_α)*g_β + A*z_α*(1-z_α)\n",
    "\n",
    "    def constraint7(sol):\n",
    "        x_α,x_β,z_α = sol[0],sol[1],sol[2]\n",
    "        return X - z_α*x_α - (1-z_α)*x_β  \n",
    "\n",
    "    con1 = {'type':'ineq', 'fun': lambda sol: sol[0]}\n",
    "    con2 = {'type':'ineq', 'fun': lambda sol: 1-sol[0]}\n",
    "    con3 = {'type':'ineq', 'fun': lambda sol: sol[1]}\n",
    "    con4 = {'type':'ineq', 'fun': lambda sol: 1-sol[1]}\n",
    "    con5 = {'type':'ineq', 'fun': lambda sol: sol[2]}\n",
    "    con6 = {'type':'ineq', 'fun': lambda sol: 1-sol[2]}\n",
    "    con7 = {'type':'eq', 'fun': constraint7}\n",
    "    cons = [con1,con2,con3,con4,con5,con6,con7]\n",
    "\n",
    "    guess = [.2,.6,.2]\n",
    "\n",
    "    sol = minimize(objective,guess,method = 'SLSQP',constraints = cons,\\\n",
    "                  tol = 1e-8)\n",
    "    #return [x_α,x_beta,z_α],[objective value]\n",
    "    return sol.x,sol.fun"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 3,
   "id": "20431d63",
   "metadata": {},
   "outputs": [],
   "source": [
    "x_α_0,x_β_0 = .25,.75\n",
    "a_α,a_β = 10,10\n",
    "b_α,b_β = 2,2\n",
    "\n",
    "num_error = 1e-7\n",
    "X_grid = np.linspace(num_error,1 - num_error,100)\n",
    "A_grid = np.linspace(0,3,40)\n",
    "feasible_points_X,feasible_points_A = [],[]\n",
    "\n",
    "for i in X_grid:\n",
    "    for j in A_grid: \n",
    "        minimum_points = two_phase_optimization(x_α_0,x_β_0,a_α,a_β,b_α,b_β,j,i)[0]\n",
    "        if minimum_points[2] < 1 - num_error and minimum_points[2] > num_error:\n",
    "            feasible_points_X.append(i)\n",
    "            feasible_points_A.append(j)"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 4,
   "id": "e65b511c",
   "metadata": {
    "scrolled": false
   },
   "outputs": [
    {
     "data": {
      "image/png": "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\n",
      "text/plain": [
       "<Figure size 432x288 with 1 Axes>"
      ]
     },
     "metadata": {
      "needs_background": "light"
     },
     "output_type": "display_data"
    }
   ],
   "source": [
    "plt.scatter(feasible_points_X,feasible_points_A)\n",
    "plt.xlabel('X')\n",
    "plt.ylabel('A')\n",
    "plt.title('Two Phase Region vs. Stress at a given Temp')\n",
    "plt.show()"
   ]
  },
  {
   "cell_type": "markdown",
   "id": "a5d595a1",
   "metadata": {},
   "source": [
    "$$\\textbf{Symbolic}$$\n",
    "\n",
    "Method of Lagrange multipliers"
   ]
  },
  {
   "cell_type": "markdown",
   "id": "3dec938b",
   "metadata": {},
   "source": [
    "$\\textbf{Minimize}$\n",
    "\\begin{align*}\n",
    "l(z^\\alpha,z^\\beta,x^\\alpha,x^\\beta,\\lambda) &=  z^\\alpha g^\\alpha(x^\\alpha) +(1-z^\\alpha)g^\\beta(x^\\beta)+ A z^\\alpha (1-z^\\alpha) \\\\\n",
    "&\\quad - \\lambda( (z^\\alpha x^\\alpha +(1-z^\\alpha)x^\\beta - X)\n",
    "\\end{align*}\n",
    "\n",
    "$\\textbf{Subject to}$\\\n",
    "$$ 0 \\leq z^\\alpha,z^\\beta,z^\\alpha+z^\\beta, x^\\alpha,x^\\beta,X \\leq 1$$\n",
    "\n",
    "Finding the partial derivatives with respect to all variables we get the following equations, \n",
    "\\begin{align*}\n",
    "    \\frac{\\partial l}{\\partial z^\\alpha} &=  g^\\alpha(x^\\alpha) - g^\\beta(x^\\beta) + A(1 - 2z^\\alpha) - \\lambda(x^\\alpha + x^\\beta) = 0\\\\\n",
    "        \\frac{\\partial l}{\\partial x^\\alpha} &=  z^\\alpha(\\frac{d g^\\alpha}{d x^\\alpha} - \\lambda) = 0\\\\\n",
    "    \\frac{\\partial l}{\\partial x_1^\\beta} &= (1-z^\\alpha)(\\frac{d g^\\beta}{d x^\\beta} - \\lambda) = 0\\\\\n",
    "    \\frac{\\partial l}{\\partial \\lambda} &= z^\\alpha x^\\alpha +(1 - z^\\alpha) x^\\beta - X = 0\n",
    "\\end{align*}\n",
    "\n",
    "In which the objective value comes in the form, \n",
    "\\begin{align*}\n",
    "& 1. g^\\alpha(X) \\implies z^\\alpha = 1 \\\\\n",
    "& 2. g^\\beta(X) \\implies z^\\alpha = 0\\\\\n",
    "& 3. \\frac{d g^\\alpha}{d x^\\alpha} = \\frac{d g^\\beta}{d x^\\beta} = \\frac{g^\\alpha(x^\\alpha) - g^\\beta(x^\\beta) + A(1 - 2z^\\alpha)}{x^\\alpha - x^\\beta} \\implies z^\\alpha = \\frac{X - x^\\beta}{x^\\alpha - x^\\beta}\n",
    "\\end{align*}"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 84,
   "id": "3b8d1fca",
   "metadata": {},
   "outputs": [],
   "source": [
    "import sympy as sy\n",
    "sy.init_printing()\n",
    "\n",
    "x_α, x_β, z_α, lam, a, b, x_α_0, x_β_0, A, X = sy.symbols('x^α x^β z^α lam a b x_0^α x_0^β A X')\n",
    "\n",
    "answer = sy.solve((a*(x_α-x_α_0)**2 - a*(x_β-x_β_0)**2 + A - 2*A*z_α +lam*(x_α-x_β),\n",
    "         2*a*z_α*(x_α-x_α_0) + lam*z_α,\n",
    "         2*a*(1-z_α)*(x_β-x_β_0) + lam*(1-z_α),\n",
    "         z_α*x_α + (1-z_α)*x_β - X), [x_α,x_β,z_α,lam])"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 85,
   "id": "1ff0e979",
   "metadata": {
    "scrolled": true
   },
   "outputs": [
    {
     "data": {
      "image/png": "iVBORw0KGgoAAAANSUhEUgAAAIgAAAAZCAYAAADqgGa0AAAAOXRFWHRTb2Z0d2FyZQBNYXRwbG90bGliIHZlcnNpb24zLjUuMSwgaHR0cHM6Ly9tYXRwbG90bGliLm9yZy/YYfK9AAAACXBIWXMAABJ0AAASdAHeZh94AAAHQklEQVR4nO2bfZBXVRnHP0uilGGbToYKvRhlxmAU4zg5CKhDL9qkyZCSL2HagMRQEaj4wrcvGYQUEQo6OhpClJbjyMhMtiVRTk2SG0uKSUpCKfhSoEhKgmx/POcndy+//e1v93d3Fze+M7+5d895zrnPc+5znrdzt665uZkD6HzYng6cAxwH/Bf4IzBd0qPdylgb6NXdDPwfYSSwCDgZOA3YDfza9uHdyVRbqCvSgtiuk9TjTJLtE4E5wCeAp4GLgaOBSZKGJ5p2yW777cBLwNmS7iue6zafPxFYCFwi6fbW6A4q8IFjgF8BL6a/64GNwOvA+yS9nKPvBfwMGA3cJunSongpErZPAlYBM4Hx6ToTOAKYnCEdbfsBSduqnLovYcG3Fsdtu/DxdG2sRFSIi7E9Augr6cVSW7pfABwOTCozbAGhHCuIhd9fMQ9YLmm2pCeAHwMjgOcl/TZDtwKYbfstVc47H2giYpHuwFBgJ7CuElHNCpIWZCrwozLdPwC2A99MJrU05mrgq8TinCvp9Vr56AzYPoaIGW7ONO8i1u3aLK2knYSluaiKeecCw4ExHZHd9jjbzbZHtndsGn8IMAj4i6TdlWj3cTG2vwicCZwIHEUEU08CiySVU4IvAw+V87+Sttm+ASgpxBzbFwPXAeuBz0p6pT3CFQHbDcAoYLSkezLtdYSif4mIOVamrtWZ4ccD6ySV2/n3Aqtt/zQpTLlnfx+4ADhV0pO1ytJBDAZ6A422hxDKPhLoA/wemFLKrlpYENt9gTuAY4EHgRuBe4D3A7fbvqLMw6YBP6/AzDxgBzDV9heAW4AtwKcl/btj8tWMacAe4LqcS/geoRy3SrqSiBP2pB+23wFMJ9LUfZCUYi1wXrl+2wvYqxyPFSNKhzA0Xd9LKEQzcBvwZ2LjrEwx5D4WpBkYIOnZbKPta4C/EdH7nEz7EODdqa8sJG21fSNwJXAX4XI+I2ljh0QrAJLW2l5KKMOFwGLbVwFTiMB5QiJdQ2yiq20vA+YCzwADbQ9sxQI0AmOAxdlG24sI5Tgb2Gq7X+raIWlHgeJVg5KCnAQMk7Sm1GF7CbEmE4FZLSyIpB155UjtW4DNRMCZxQhgbRXp3YrM/fmS1lYlRufiGiJI+5btScB3gF8CF0raAyDp74R7nEAElNuB04nA7sFW5l0DDEtZWhaXERbpAcKCln5TixOpapQymMuzypGwMF0HQ86C2H4nkXGcSVT8DqOlG8pPNojYUa3C9tHAskzTR2ipMBVheyNhCqvFMkkXtEUk6Wnb8wnLdgPwB+AcSa/l6GYBs3LDT64w9WZi3foD/8jMU1cV9zm0If9vbOfb7pA0rsJ8vYmXvwlYUoakZCD6QEZBbJ8ANBAuYzVwJ5Gj7yJikIsI/5pFf+CfFZipB+4nBJwBXEHEIgsl/ae1cTlsIHZ6tdjcDtoXMveXFBQwb0/XY8goSA2YD9Tn2oYAZxHx4sZcX1Mb8w0GDgbuayWDKSnjJmhpQZYmRk6VtCo7wvbMdPtwbrJDgZcpA9t9gOWJoZmSvm37MMKkXkYEhG1C0unV0LUXtscmHp4F+gFfS3zVipKCHFrAXEian2+zPY5QkMX5d1UFSu5lYyv9n0/XBkjuw/YA4ARgVRnlqCeCU9i36rab0MYWSJnBT4hc/xZJSl3XA68A02y/rRppOgO2zyB23zpC7seBS21/uAztRNtP2d5pu9H2KW1MX1qPXYUyXRxKAeo+Z0C2jyKKlk+QVRD2mvBjk48qDTiCyDz6E8rQlJtzG+Fv81hIaOK9RDQMgKQXiAOrI9mbKXQpbA8D7ibOVD6ZeLqWsKbfzdGeC/yQiEE+RqSEv7D9ngqPKK1Hd5XQ20JJQcbafsPKpULmEuAQYHLJ/fSCN17cSuCDwEO2r09p4HrCZO4BHitT/NlAzj86oqbxRJQ/tkylcC5hRS63/dbaZG0fbH+UCJBfAkal7AxJdxPu86ychZhCmPFbJf1V0mQi86jkiuqJcsFTnSBCTbB9EOHy1wCvAU2256b6zONEhvYNSfeXxmQzlPOIOGQAsQAfIBZoVqLLxx8AfyIqiyUGJhDB6KPA58pVEyU9D9xEBMNddgZjeyCRxjYDn5K0IUcyPV3nJvqDid3WkKNroHIWczyxmbq6tlENBhHZycOEMjxCvINxhLs9TdKC7ICajvtTWrwFeFf+tPbNjpSePwOMkPS7TPsMopZzXCvjZgO9JXVHfaNw1HRYl462lxPBaE9FfgfVlWnLYjgRAPcIFHHcPwM4v4B59jf8i/iWpV+u/UjguXIDbH8I2CTpkU7mrctQs4JIWg88Z7s91c79Hqmi2kgcXmUxiqi6lsNXiA3TY1DUF2VXESVrtUX4JsM8YKnt1USKO5741PDmPKHtoUBjNx7hdwoK+aJM0qvATfv7B7jthaS7gK8TB3tNwCnAGZI2lSF/VdKdXcdd16DQj5YPoOfhwL89HEBF/A9fyZqWGPjDzAAAAABJRU5ErkJggg==\n",
      "text/latex": [
       "$\\displaystyle a \\left(X - x^{α}_{0}\\right)^{2} + b$"
      ],
      "text/plain": [
       "              2    \n",
       "a⋅(X - x_0__α)  + b"
      ]
     },
     "execution_count": 85,
     "metadata": {},
     "output_type": "execute_result"
    }
   ],
   "source": [
    "#solution 1 (same as solution 2) - In the form g^α(X)\n",
    "solution1 = sy.simplify(answer[0][2]*(a*(answer[0][0]-x_α_0)**2+b) + (1-answer[0][2])*(a*(answer[0][1]-x_β_0)**2 +b) + A*answer[0][2]*(1-answer[0][2]))\n",
    "solution1"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 86,
   "id": "4a048364",
   "metadata": {},
   "outputs": [
    {
     "data": {
      "image/png": "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\n",
      "text/latex": [
       "$\\displaystyle a \\left(X - x^{β}_{0}\\right)^{2} + b$"
      ],
      "text/plain": [
       "              2    \n",
       "a⋅(X - x_0__β)  + b"
      ]
     },
     "execution_count": 86,
     "metadata": {},
     "output_type": "execute_result"
    }
   ],
   "source": [
    "#solution 4 (same as solution 5) - In the form g^β(X)\n",
    "solution4 = sy.simplify(answer[3][2]*(a*(answer[3][0]-x_α_0)**2+b) + (1-answer[3][2])*(a*(answer[3][1]-x_β_0)**2 +b) + A*answer[3][2]*(1-answer[3][2]))\n",
    "solution4"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 87,
   "id": "6c4f8d0f",
   "metadata": {},
   "outputs": [
    {
     "data": {
      "image/png": "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\n",
      "text/latex": [
       "$\\displaystyle \\frac{- \\frac{A^{2}}{4} - A X^{2} a + A X a x^{α}_{0} + A X a x^{β}_{0} - A a x^{α}_{0} x^{β}_{0} - A b + a b \\left(x^{α}_{0}\\right)^{2} - 2 a b x^{α}_{0} x^{β}_{0} + a b \\left(x^{β}_{0}\\right)^{2}}{- A + a \\left(x^{α}_{0}\\right)^{2} - 2 a x^{α}_{0} x^{β}_{0} + a \\left(x^{β}_{0}\\right)^{2}}$"
      ],
      "text/plain": [
       "   2                                                                          \n",
       "  A       2                                                                   \n",
       "- ── - A⋅X ⋅a + A⋅X⋅a⋅x_0__α + A⋅X⋅a⋅x_0__β - A⋅a⋅x_0__α⋅x_0__β - A⋅b + a⋅b⋅x_\n",
       "  4                                                                           \n",
       "──────────────────────────────────────────────────────────────────────────────\n",
       "                                                  2                           \n",
       "                                     -A + a⋅x_0__α  - 2⋅a⋅x_0__α⋅x_0__β + a⋅x_\n",
       "\n",
       "                                         \n",
       "    2                                   2\n",
       "0__α  - 2⋅a⋅b⋅x_0__α⋅x_0__β + a⋅b⋅x_0__β \n",
       "                                         \n",
       "─────────────────────────────────────────\n",
       "    2                                    \n",
       "0__β                                     "
      ]
     },
     "execution_count": 87,
     "metadata": {},
     "output_type": "execute_result"
    }
   ],
   "source": [
    "#wanted solution - Two phase solution \n",
    "wanted_solution = sy.simplify(answer[2][2]*(a*(answer[2][0]-x_α_0)**2+b) + (1-answer[2][2])*(a*(answer[2][1]-x_β_0)**2 +b) + A*answer[2][2]*(1-answer[2][2]))\n",
    "wanted_solution"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 88,
   "id": "c0edb729",
   "metadata": {},
   "outputs": [
    {
     "data": {
      "image/png": "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\n",
      "text/latex": [
       "$\\displaystyle - 2 a \\left(X - x^{α}_{0}\\right) \\left(x^{α}_{0} - x^{β}_{0}\\right)$"
      ],
      "text/plain": [
       "-2⋅a⋅(X - x_0__α)⋅(x_0__α - x_0__β)"
      ]
     },
     "execution_count": 88,
     "metadata": {},
     "output_type": "execute_result"
    }
   ],
   "source": [
    "#First boundary where two phase solution exists\n",
    "boundary_1 = sy.solve(wanted_solution - solution1, [A])\n",
    "boundary_1[0]"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 89,
   "id": "f5c47f74",
   "metadata": {},
   "outputs": [
    {
     "data": {
      "image/png": "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\n",
      "text/latex": [
       "$\\displaystyle 2 a \\left(X - x^{β}_{0}\\right) \\left(x^{α}_{0} - x^{β}_{0}\\right)$"
      ],
      "text/plain": [
       "2⋅a⋅(X - x_0__β)⋅(x_0__α - x_0__β)"
      ]
     },
     "execution_count": 89,
     "metadata": {},
     "output_type": "execute_result"
    }
   ],
   "source": [
    "#Second boundary where two phase solution exists\n",
    "boundary_2 = sy.solve(wanted_solution - solution4, [A])\n",
    "boundary_2[0]"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 90,
   "id": "ccd04f83",
   "metadata": {},
   "outputs": [],
   "source": [
    "boundary_one = sy.lambdify([a,X,x_α_0,x_β_0],boundary_1[0])\n",
    "boundary_two = sy.lambdify([a,X,x_α_0,x_β_0],boundary_2[0])"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 93,
   "id": "1178078e",
   "metadata": {
    "scrolled": false
   },
   "outputs": [],
   "source": [
    "def check(A,a,X,x_α_0,x_β_0):\n",
    "    if A <= boundary_one(a,X,x_α_0,x_β_0) and A <= boundary_two(a,X,x_α_0,x_β_0):\n",
    "        return True\n",
    "    else:\n",
    "        return False\n",
    "\n",
    "X_grid = np.linspace(num_error,1 - num_error,100)\n",
    "A_grid = np.linspace(0,3,40)\n",
    "# A = np.linspace(0,1,200)\n",
    "# X = np.linspace(0,1,200)\n",
    "a, b, x_α_0, x_β_0 = 10, 2, .25, .75\n",
    "feasible_points_X,feasible_points_A = [],[]\n",
    "\n",
    "for i in X_grid:\n",
    "    for j in A_grid: \n",
    "        if check(j,a,i,x_α_0,x_β_0):\n",
    "            feasible_points_A.append(j)\n",
    "            feasible_points_X.append(i)"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 94,
   "id": "86470e0d",
   "metadata": {
    "scrolled": true
   },
   "outputs": [
    {
     "data": {
      "image/png": "iVBORw0KGgoAAAANSUhEUgAAAYIAAAEWCAYAAABrDZDcAAAAOXRFWHRTb2Z0d2FyZQBNYXRwbG90bGliIHZlcnNpb24zLjUuMSwgaHR0cHM6Ly9tYXRwbG90bGliLm9yZy/YYfK9AAAACXBIWXMAAAsTAAALEwEAmpwYAAAkuElEQVR4nO2de7gkZX3nP18HMMNFLplR5MCZ8YIYUcPICBI0zqoRIbKOgWcDurKwG1E2JGrUiD6usoiRBDZCFmVgkSWuCroCI6tExVUQr5HhInJzkdtcUMQRGWBUBn/7R9UZes509Tndp6qr6q3v53n6Od1V1fV9f1XV/et63+/5vYoIjDHGdJcn1d0AY4wx9eJEYIwxHceJwBhjOo4TgTHGdBwnAmOM6ThOBMYY03GcCFqMpGMlfavudlSFpJslLau7HaZ8JL1P0vl1t8NkOBGMgKSHex6/k7Sx5/UbS9Y6WdJj+b4flPQdSQeVqTEXJF0o6bd5+9ZLulLSc8vYd0TsGxFXlbGvqpG0p6RLJD0g6VeSbpJ0bL5usaSQtE3NzSxk3G2MiL+LiL8YhxaApJf1fEYfyWPt/RxPjqstTcSJYAQiYsepB3AvcHjPsk9XIPnZXGsh8C3gUkmqQGdU/iFv3wSwFvhEze2pg/8FrAYWAb8PHAP8bLZvbnKSSIGIuKbnM7tvvniXns/tvXW2r26cCEpC0u/ldwYL8tfvl7RJ0lPy16dKOjN/vrOkT0r6uaR78m1nPBcR8Rjwz8DuZF82U9pnSPqlpLskHdqz/DhJt0raIOlOSW/pWbdA0hfzu4z1kq6ZaoOkPfJftz/P9/nXszkGEbER+BywX49O4b4kzZf0z3nbb5X0t5LW9Ky/W9Kr8udPlnSmpHX540xJT87XLZO0RtI7Jd0v6T5Jx/Vro6SjJF07bdk7JF2ePz9M0i35MVsr6V2ziR14MXBhRDwSEZsi4vqI+Jd83Tfzvw/mvz4Pyrv1vi3po5LWAyfnMZ4h6V5JP5O0QtL8vF2Dztd78rZukHS7pFcWxP6nkq6X9JCk1ZJO7lm9VRv7vP8ASd/N23CfpLMlbVd0QCQdk1/fv5D0X6adz5MlfSp//mVJJ057742S/ix//lxld5rr8/j+Xc92F0r6mKQv5fF/X9KzitpU0M6dJX0ij2mtss/qvHxd73l6MP8c/VG+fHV+vf2Hae1Zkbd3g6SrJS0apj21EBF+zOEB3A28Kn/+TeCI/PlXgZ8Ah/ase33+/JPAF4CdgMXAj4H/VLD/k4FP5c+fDJwOrM5fHws8BrwZmAecAKwDlK//U+BZgICXA48CL8rXfQRYAWybP16Wb/ckYBXwAWA74JnAncAhBe27EDg1f74D2S/jG/PXA/cFnAZcDewK7An8EFhTcGxPAb4HPJXszug7wIfydcuATfk22wKH5bHu2qe92wMbgL17lv0AOCp/fh/wsvz5rlPHaxbXwdeAbwNHAZPT1i0GAtimZ9mxeZv/CtgGmA+cCVwO7JZfG/8H+MgM52sfsjuRPXq0nlXQxmXAC/Lz8kKyO5blRW3s8/79gZfk7V0M3Aq8vWDb5wEPAy/Nz/0ZZNfq1Pk8mSeu62OAb09774Nk1/sOeXzH5bovAh4A9u25/tYDB+TrPw1cPMO52iJWYCVwbq71VOBfgbdMO0/HkX3GTiXrBfhY3r5Xk11PO/a0ZwPwx/n6s4Bv1f09NeP1W3cD2v5gyy+rDwH/lF+QPwXeRvZl93vARmBBfjH9Bnhezz7eAlxVsP+Tgd/mH4z7ga8D++frjgXu6Nl2+/wC371gXyuBt+XPTyFLRs+ets2BwL3Tlr0X+J8F+7wQ+HXevt8BdwEvnM2+mJZggL+gOBH8BDisZ90hwN3582X58e39or0feElBmz8FfCB/vnf+wd0+f31vfj6eMuR1sGt+rm8GHgduAF6cr1tM/0Rwb89rAY/Q8yUOHATcNcP5enYe66uAbYds85nAR4vaOIv3vx24rGDdB4CLpl2bv6V/Itgpj31R/vrDwAX58z8Hrpm273OBD/Zcf+f3rDsMuG2Gdm+OFXga2edxfs/6o4Fv9Jyn/9ez7gX5e5/Ws+wXwH497bm4Z92O+fWw1zDnZtwPdw2Vy9VkX0ovAm4CriT7Jf4Ssi/sB8iSwXbAPT3vu4esf72Iz0XELhHx1Ih4RUSs6ln306knEfFo/nRHAEmHSvpefkv9INmHZEG+zenAHcBX89vdk/Lli4A98tvgB/P3vY/sA1PEGRGxC9kHbCPZr9TZ7GsPsl97U/Q+n84ebH3M9uh5/YuI2NTz+lHy49CHz5B92AHeAKzsOXZHkB2ne/Lb+lkNzEfELyPipIjYlyy+G4CV0sCxnN54F5J9Wa7qOVZfzpdDwfmKiDvIvpBPBu6XdLGk3uOyGUkHSvqGsm66XwFv5YnrYUYkPSfvnvqppIeAvxvw/i3ObX58f9Fvw4jYAHyJ7G6K/O/UWNsi4MBp19AbybpHp/hpz/NB570fi8jusO7r2f+5ZHcGU/SO9WzM2zx9Wa9mb9wPk92x9D0nTcGJoFy+Q/Yl+Hrg6oi4BZgk66K5Ot/mAbJb5N5+w0myQdbSUNZ/fgnZLfnT8i/qK8h+eRIRGyLinRHxTOBw4G/yvuXVZL9Cd+l57BQRh82kGdmA29uAs/K+7Zn2dR9Zl9AUew3Y/Tq2PmbrZjwQ/fkqsEDSfmQJ4TM9MfwgIl5H9kWwkmzMYyjyhH8G2Yd/N7JfkH037Xn+ANkXyr49x2rnyAY3B50vIuIzEfFSsuMTwN8X6H2GrOtpr4jYmayraSpRzaYM8TnAbWTdak8hS+pFiW6Lc5tfD79fsC3ARcDReeKdD3wjX76a7LPUew3tGBEnzKK9s2E12R3Bgp79PyVP6KOy+TqWtCPZNTDqtToWnAhKJP/Vswr4S5744v8OWVfD1fk2j5N9uXxY0k75QNLfkHVXlMl2ZH2UPwc2KRtEfvXUSkmvlfTs/BfrQ2S3r4+T9Y8+lA9Azpc0T9LzJb14NqIRcSXZRX/8LPb1OeC9knaVNAGcWLBbyL4o3i9pobIB+Q8w4jHL7xw+T/YrezeyOzckbSfpjZJ2jmxgfuq4zIikv89j20bSTmTjNXdExC/IzsHvyMZIitr0O+B/AB+V9NR8nxOSDsmf9z1fkvaR9Io88f+aLJkUtXknYH1E/FrSAWR3Q1PM2Mb8/Q8BDyuzCA/6Mv48cHg+sLod8F8pThqQ/UhZRNYF9tn8eAB8EXiOpDdJ2jZ/vFjSHwzY16yJiPvIfhj8N0lPkfQkSc+S9PI57PYwSS/N4/4Q8P2IGHS3WztOBOVzNdmt5r/2vN6JJ1wZkA0QPkLWR/4tsl9qF5TZiPx2+6/Jvmx/Sfahv7xnk73JBjgfBr4LfDwirsoT1eFkzp+7yH6png/sPIT86cDfkvXBDtrXKcCafN3XyL48flOwz1OBa8kGlG8CrsuXjcpnyPrV//e0LqU3AXfnXR9vBf49gKRJDfabbw9cRjZWcifZl9q/hc0/ED4MfDvvfnhJwT7eQ9b9871c/2s80c3W93yRJfvTyI7tT8nuZN5XsP//DJwiaQNZIt18tzPLNr6L7DraQJa0PlugQ0TcTHadX0x2d7CBbCyj7/mNiN8Al5Kdk947tA1kP2COIvuB8VOyO54nF2mPwDFkP5xuIfusfB54+hz29xngg2RdQvuTdWU1mil3iTG1I+kEMvfOXH6NmQaSd5E8SNatdFfNzakMSReSGR7eX3dbhsF3BKY2JD1d0sH57fg+wDvJflWbBJB0uKTtJe1ANmZyE5kTzDQMJwJTJ9uROTQ2kNlivwB8vNYWmTJ5HVl3zjqyrq2jwl0QjaSyriFJe5H949TuZINQ50XEWdO2WUb24Z+6Vbw0Ik6ppEHGGGP6UmV9k03AOyPiutxFsUrSlbmlspdrIuK1FbbDGGPMACpLBLkt6778+QZJt5L909T0RDAUCxYsiMWLF8+9gcYY0yFWrVr1QEQs7LduLBUPJS0GlgDf77P6IEk3kvUjviu3nU1///FkvnQmJye59tprp29ijDFmAJLuKVpX+WBxbhu7hKw41UPTVl9HVl/kD4H/TvafnFsREedFxNKIWLpwYd+EZowxZkQqTQSStiVLAp+OiEunr4+Ih/JaHETEFcC2+X+NGmOMGROVJYL8X+E/AdwaEf9YsM3uU0W58n95fxIFhamMMcZUQ5VjBAeT/bv+TZJuyJe9j6xYGBGxAjgSOEHSJrIaKfYZG2PMmKnSNfQtBheZIiLOBs6uqg3GGGNmxvOkGjMCK69fy+lfuZ11D25kj13m8+5D9mH5kokZ1xnTRJwIjBmSldev5b2X3sTGx7Jqz2sf3Mh7L71p8/qidU4Gpqk4ERgzJKd/5fbNX/RTbHzscU7/yu2bn/db50RgmooTgTFDsu7BjUMtn2mdMXXj6qPGDMkeu8wvXD5onTFNxYnAmCF59yH7MH/beVssm7/tPN59yD4D1xnTVNw1ZMyQTPX1D3IG2TVk2oQTgTEDKNMKalupaSqtm7N46dKl4eqjZhxMt4lC1s1zxP4TXLJq7VbLP/JnLwAY+j1OBmYcSFoVEUv7rnMiMKY/B5/2ddb2cfvMk3i8z+dmIh8QHvY93z7pFSW01pjBDEoE7hoypoAiy2e/L/RB24/6HmPGhV1DxhRQZPmcp/4ltAbZRwe9x5i6cSIwpoAiK+jRB+41tH100HuMqRt3DRlDsaPn2nvWc9H3V/N4BPMkjth/glOXZ4PC05dPDfoO+x67iUzdOBGYzlNURO7ae9Zzyaq1m/v3H4/gklVrAfouX7pot8J1My13kTpTJ3YNmc4zrDuoTNeQ3URmXNg1ZMwAhnUHlekaspvINAEPFpvOM6zTp0zXkN1Epgk4EZjOM6zTp0zXkN1Epgk4EZjOs3zJBEfsP7H513mv02eY5cuXTJS6L2PGhccITOdZef3akZw+ZbqG+u3LycCMC7uGTOexa8h0AbuGjBmAXUOm63iMwHQeu4ZM13EiMJ3HriHTddw1ZDrPoKknly7abajlU5S5L2OqxonAdIo2FXhrU1tNu7FryHSGYaeeHHb5KFNVjqLhZGBGwVNVGkN5NtFx2EdtKzVlY/uoMZRnEx2HfdS2UjNO7BoynaEsa+c47KO2lZpx4kRgOkNZ1s5x2EdtKzXjpLKuIUl7AZ8Edgd+B5wXEWdN20bAWcBhwKPAsRFxXVVtMt2hjKknh10+ylSVo2rYUWTKpMoxgk3AOyPiOkk7AaskXRkRt/Rscyiwd/44EDgn/2vMyJQ19eSwy8ssOjeTRr/4wNNbmtEYm2tI0heAsyPiyp5l5wJXRcRF+evbgWURcV/RfuwaMjNRtTuobtdQkYYdRWYQtbuGJC0GlgDfn7ZqAljd83pNvmyLRCDpeOB4gMnJycraadKgandQU11DdhSZUal8sFjSjsAlwNsj4qHpq/u8ZatPQEScFxFLI2LpwoULq2imSYiqnTt1u4aKNOwoMqNSaSKQtC1ZEvh0RFzaZ5M1wF49r/cE1lXZJpM+VTt36nYNFWnYUWRGpUrXkIBPALdGxD8WbHY5cKKki8kGiX81aHzAmNlQZhG5OovOjaJhzChUOUZwMPAm4CZJN+TL3gdMAkTECuAKMuvoHWT20eMqbI9JDFsot8bHxIyCaw2ZVlJUQG4chd/qLjo3rIYL1Rlw0TmTIEUW0XFYOJtqH3WhOjOI2u2jxpRNkVVyHBbOptpHXajOjIprDZlWMshC2VX7qAvVmVFxIjCtZJCFsqv2UReqM6PiRGBayfIlExyx/8TmX8G9RdmK1p26/AWVLh+H9igaHig2M+ExAtNKVl6/trbCb0XLx1l0btj3OBmYQdg1ZFqJXUN2DZnhsGvIJIddQ+W8xxjwGIFpKXYNDfceYwbhRGBaiV1Ddg2Z8nDXkGk8w0w7OY7pIpswVeUwGsuXTLgGkRmIE4FpNMNOO2nXUPFyT21pirBryDSaYaedtGtouH3ZTdQd7BoyrWXYaSftGipnX6ZbeLDYNJpRHDJ2Dc1+X8aAE4FpOKM4ZOwamv2+jAF3DZmGM8q0k1N0carKUfdluo0TgWkMtjiOHx9zA3YNmYZQNPVkE6eLbNtUlaNoOBmkh6eqNI1nWJuo7aPVathWmh62j5rGM6xN1PbRejRMmtg1ZBpBmZZI20fnrmG6hROBaQRlWiJtH527hukW7hoyY2eYInJNLPzWtqJzo2jYTdQtnAjMWBm2iBw0r/BbndrjjM9F6rqDXUNmrJTlDrJrqJ747CZqL3YNmcZQljvIrqHqNOwm6h4eLDZjJQVXTd2OnjrjM2niRGDGSgqumrodPXXGZ9LEXUOmMqp0B9k1VF98dhSlhxOBqYSq3UFFy+0aql6733kFO4rajF1DphKqdgc11VVTtXZT47OjqPnU4hqSdAHwWuD+iHh+n/XLgC8Ad+WLLo2IU6pqjxkvVbuD2uaqaZPGKNp2FLWbKgeLLwReM8M210TEfvnDSSAhUnbudNk1VKRtR1G7qSwRRMQ3gfVV7d80m5SdO112DRVp21HUbuoeLD5I0o3AOuBdEXFzze0xJTHKFJNNnMqxidpNjc+0lzoTwXXAooh4WNJhwEpg734bSjoeOB5gcnJybA00xpguUKlrSNJi4Iv9Bov7bHs3sDQiHhi0nV1D7aCsqSebOJVj3dNINjE+T2/ZfGqbqnJQIpC0O/CziAhJBwCfJ7tDGNggJ4J2YPtoezVsH02TuuyjFwHLgAWS1gAfBLYFiIgVwJHACZI2ARuBo2ZKAqY92D7aXg3bR7tHZYkgIo6eYf3ZwNlV6Zt62WOX+bX8at2jxl/M49Buany2j7YbF50zldBEi2MK2k2Nz/bRdlO3fdS0nKICZFUXl2tyUbbUi8710/b0lu3GicCMTFFhuSlSLfxWp3bT43MxunbionNmZIqcQU11tqSg3bb47CZqDp6q0lRCkVOkqc6WFLTbFp/dRO3Ag8VmZAYVIGtiYbQUtNsWn91E7cCJwIzMIAdJE50tKWi3LT67idqBu4bMyAwqLDdF0wqjpaDdtvhM83EiMLPC1kAzKr52mo9dQ2ZGhi0g19TCaClopxKfi9SNn9qKzlWBE8H4GbaAXNssjm3STiU+20rHj+2jZk4MW0CubRbHNmmnEp9tpc3CriEzI6NYBttkcWyTdirx2VbaLJwIzIyMYhlsk8WxTdqpxGdbabNw15DZgiKHxzBFzppaGC0F7VTic5G6ZuFEYDZTVETu2nvWJ1UYrc3aqcXnInXNwK4hs5myppdMxdnSRO0uxGc3UTXYNWRmRVnTS6bibGmidpfjM9Ux9GCxpIMlfayKxph6KdMRkoKzpYnaXYjPjJ9ZJQJJ+0n6B0l3A6cCt1XaKlMLZTpCUnC2NFG7C/GZ8VPYNSTpOcBRwNHAL4DPko0p/Jsxtc2MmbKml0zF2dJE7a7EZ8bLoDGC24BrgMMj4g4ASe8YS6tMLay8fm1j3SXW7k58Tgbjp9A1JOn1ZHcEfwR8GbgYOD8injG+5m2NXUPVYddQ87W7EJ9dQ9UwkmsoIi4DLpO0A7AceAfwNEnnAJdFxFeraKypD7uGmq/d5fhMdcw4WBwRj0TEpyPitcCewA3ASVU3zIwfu4aar92F+Mz4Gco+GhHrI+LciPC9W4LYNdR87S7EZ8aP/6HMbGbQ1JOpT6fYJu2uxGfGhxNBR3HBL9NUfG2OH9ca6iDDTj3Z1ekUm6idenye2rI6PFWl2YKybKJdtTjWba+sWqOp2raVzg0XnTNbUJZNtKsWx6baK1OIz7bSevAMZR3EFsf2aqcen22l9eBE0EFscWyvdurx2VZaD5V1DUm6AHgtcH9EPL/PegFnAYcBjwLHRsR1VbWnq5Qx9WQTi5N1VTv1+GbStqOoGqocI7gQOBv4ZMH6Q4G988eBwDn5X1MSZU09Oezy1Auj1amdenwzafe7nsHTW86VSl1DkhYDXyy4IzgXuCoiLspf3w4si4j7Bu3TrqHZU7U7qKnukpS1U49vFG07imZHU11DE8Dqntdr8mVbJQJJxwPHA0xOTo6lcSlQtTuobe6SFLRTj28UbTuK5k6dg8X97AF9r4KIOC8ilkbE0oULF1bcrHToqrskZe3U4xtF246iuVNnIlgD7NXzek9gXU1tSZKuuktS1k49vlG07SiaO3V2DV0OnCjpYrJB4l/NND5g+lPkpKjaHdRkd0mq2qnHN4r28iUTdhPNkSrtoxcBy4AFktYAHwS2BYiIFcAVZNbRO8jso8dV1ZaUKXIGTdFVd0mq2qnHNxdtu4lGx7WGWk6RM6ipDg9rN18jFW27ibakqa4hUwJFjommOjys3XyNVLTtJpo9LjHRcgY5KZro8LB28zVS0babaPY4EbScQU6KJjo8rN18jVS07SaaPe4aajmDppecItUpDbuqnXp8ZWqb2eE7AmOM6Th2DbWcomkn655W0Nrt1UhF29NbbomnqkwY20e7pZ16fLaPVoftowlj+2i3tFOPz/bRevAYQcuxfbRb2qnHZ/toPTgRtBzbR7ulnXp8to/Wg7uGWsQw0042tUCYtZuvkYq2i9HNHieCljDstJNNLxBm7eZqpKbtYnQzY9dQSxh22sm2OTys3RyN1LW76iayaygBhp12sm0OD2s3RyN1bbuJtsaDxS1hFMdEmxwe1m6ORuradhNtjRNBSxjFMdEmh4e1m6ORurbdRFvjrqGWMKi4XCoFwqzdHI3Utc2WOBE0EFvejKkWf8a2xK6hhlFURC71AmHWbo5GV7VTL1LnonMtYlibaOpWP2uPX6Or2qnbSm0fbRHD2kRTt/pZe/waXdXusq3UrqGGUaZFLgWrn7XHr9FV7S7bSp0IGkaZFrkUrH7W7lZ8dR/bruKuoRoZpohc6gXCrN0cja5qT2l00VHkRFATwxaRg24UCLN2/Rpd1Z7S6Pe5hLQL1dk1VBNluYO66vDoqnbq8TX12KbgKLJrqIGU5Q4qWp66w6Or2qnH19Rjm7qjyIPFNWF3ibWbqtFV7UEaqTuKnAhqwu4SazdVo6vagzRSdxQ5EdTE8iUTHLH/xOZfJ72OhjKWL18yUbmGtcevnXp8TT22KQ8Ug8cIamPl9Wtb77Kwtl1DKWkP0li6aLekk4FdQzVh15C1m6rRVW27hqoTfg1wFjAPOD8iTpu2fhnwBeCufNGlEXFKlW1qCnYNWbupGl3VtmuoAiTNAz4GHAo8Dzha0vP6bHpNROyXPzqRBMCuIWs3V6Or2nYNVcMBwB0RcWdE/Ba4GHhdhXqtwu4SazdVo6vaXXYNVdk1NAGs7nm9Bjiwz3YHSboRWAe8KyJunr6BpOOB4wEmJycraOr4GWXqya5OK2jtbsXX1GObMlUmgn73X9M7564DFkXEw5IOA1YCe2/1pojzgPMgGywuuZ2V0sUCVsakSMqf5cpcQ5IOAk6OiEPy1+8FiIiPDHjP3cDSiHigaJs2uYaKpp30lIbWbrJGV7VH0WjT9Ja1TFUpaRvgx8ArgbXAD4A39Hb9SNod+FlEhKQDgM+T3SEUNqpNiaDIIlq3FS4Fq19XtVOPr23Htk220lrsoxGxSdKJwFfI7KMXRMTNkt6ar18BHAmcIGkTsBE4alASaBtFlrO6rXApWP26qp16fG07tqnYSiv9P4KIuAK4YtqyFT3PzwbOrrINdbLHLvP7/rrYYwy/bsahYe3xa6ceX9uObSq2UtcaqpBBVjTbDK3dVI2uao+ikYqt1LWGSmKYaSc9paG1m6zRVe1RNJYvmUjCTeREUALDTjtZdwEta7dXO/X42nps2z61pYvOlcCwBeSa6oCwdvO1U48vlWPbRDeRp6qsmGELyDXVAWHt5munHl8qx7ZtbiIPFpfAsMWw6i6gVZaGtcevnXp8qRzbtrmJnAhKYBSnQRMdENZuvnbq8aVybNvmJnLX0JAM4w5qmwPC2s3XTj2+VI5t29xETgRDMKw7CNrpgLB2c7VTjy+1Y9sWN5FdQ0NQ1vSSbXNAWLs52qnHl/qxrdNNZNdQSZQ1vWTbHBDWbo526vGlfmyb6ibyYPEQlOkoaJMDwtrN0U49vtSPbVPdRE4EQ1Cmo6BNDghrN0c79fhSP7ZNdRO5a2gIypxecoq2T+1nbZ/XlLTHqdEkfEdgjDEdx66hISiaejKVafes3Xzt1ONL/djWObVlLVNVVoXto83RsPb4tVOPL/Vja/toAtg+au26tVOPL/Vja/toAtg+au26tVOPL/Vja/toAtg+au26tVOPL/Vja/toyyijuFwqBbSs3Rzt1ONL/dg2tRidE0EfyiouV7S8rQW0rF2/durxdeXYNq0YnV1DfSjLHVS3O6FqDWuPXzv1+Lp8bKt2E9k1NCRluYOKltftTihLw9rj1049Ph/bevBgcR9ScSek7PDoqnbq8XX52NaJE0EfUnEnpOzw6Kp26vF1+djWibuG+lBmcbkmFLdKtUBYV7VTj6/rx7YOOp8ImmjlMsZ0k7q+jzrtGiqriFxTi1tVrWFtn9eUtJsaX1mF6lx0roCqbaJdtsJZu70aXdVuanxlWUttHy2gapuorXDWbqNGV7WbGt84rKWddg3ZCmfttmmnHp+Pbf91VdPpRGArnLXbpp16fD629VhLK00Ekl4j6XZJd0g6qc96SfqnfP0PJb2oinasvH4tB5/2dZ5x0pc4+LSvs/L6rObH8iUTHLH/xOYsPU9PFIyqcvnyJROVa49Dw9o+rylpNzW+qUJ1/b7DyqKyMQJJ84CPAX8CrAF+IOnyiLilZ7NDgb3zx4HAOfnf0igqIDeFC2hZu03aqcfnY1tPobrKXEOSDgJOjohD8tfvBYiIj/Rscy5wVURclL++HVgWEfcV7XdY11CRM6ipDoE2aVh7/Nqpx+djO3vtYd1EdbmGJoDVPa/XsPWv/X7bTABbJAJJxwPHA0xOTg7ViKIR96Y6BNqkYe3xa6cen49tOe8ZlirHCPoNj0+PaDbbEBHnRcTSiFi6cOHCoRoxaCS+iQ6BNmlYe/zaqcfnYzvce8qiykSwBtir5/WewLoRtpkTg0bim+gQaJOGtcevnXp8Prb1FKqrsmvoB8Dekp4BrAWOAt4wbZvLgRMlXUzWbfSrQeMDozCogNwULqBl7TZppx6fj+34C9VVWmJC0mHAmcA84IKI+LCktwJExApJAs4GXgM8ChwXEQNHgscxQ5kxxqRGbSUmIuIK4Ippy1b0PA/gL6tsgzHGmMF0+j+LjTHGOBEYY0zncSIwxpiO40RgjDEdp3UT00j6OXBP3e0YkgXAA3U3Ysx0MWboZtxdjBnaF/eiiOj7H7mtSwRtRNK1RbatVOlizNDNuLsYM6QVt7uGjDGm4zgRGGNMx3EiGA/n1d2AGuhizNDNuLsYMyQUt8cIjDGm4/iOwBhjOo4TgTHGdBwngpKQ9BpJt0u6Q9JJfda/TtIPJd0g6VpJL62jnWUzU9w9271Y0uOSjhxn+6pgFud6maRf5ef6BkkfqKOdZTObc53HfoOkmyVdPe42ls0szvW7e87zj/JrfLc62jonIsKPOT7Iymz/BHgmsB1wI/C8advsyBNjMi8Ebqu73eOIu2e7r5NVoj2y7naP4VwvA75Yd1triHsX4BZgMn/91LrbXXXM07Y/HPh63e0e5eE7gnI4ALgjIu6MiN8CFwOv690gIh6O/GoBdqDPlJwtZMa4c/4KuAS4f5yNq4jZxpwas4n7DcClEXEvQES0/XwPe66PBi4aS8tKxomgHCaA1T2v1+TLtkDS6yXdBnwJ+I9jaluVzBi3pAng9cAK0mBW5xo4SNKNkv5F0r7jaVqlzCbu5wC7SrpK0ipJx4ytddUw23ONpO3JJti6ZAztKp1KJ6bpEP1ml97qF39EXAZcJumPgQ8Br6q6YRUzm7jPBN4TEY+rYBLuljGbmK8jq+vycD5L30pg76obVjGziXsbYH/glcB84LuSvhcRP666cRUxq891zuHAtyNifYXtqQwngnJYA+zV83pPYF3RxhHxTUnPkrQgItpUtGo6s4l7KXBxngQWAIdJ2hQRK8fSwvKZMeaIeKjn+RWSPt6Rc70GeCAiHgEekfRN4A+BtiaCYT7XR9HSbiHAg8VlPMgS6p3AM3hiUGnfads8mycGi18ErJ163dbHbOKetv2FtH+weDbneveec30AcG8XzjXwB8D/zbfdHvgR8Py6215lzPl2OwPrgR3qbvOoD98RlEBEbJJ0IvAVMqfBBRFxs6S35utXAEcAx0h6DNgI/HnkV1FbmWXcSTHLmI8ETpC0iexcH9WFcx0Rt0r6MvBD4HfA+RHxo/paPTeGuL5fD3w1sjuhVuISE8YY03HsGjLGmI7jRGCMMR3HicAYYzqOE4ExxnQcJwJjjOk4TgTGzAFJe0m6a6ripKRd89eL6m6bMbPFicCYORARq4FzgNPyRacB50XEPfW1ypjh8P8RGDNHJG0LrAIuAN4MLImsWqUxrcD/WWzMHImIxyS9G/gy8GonAdM23DVkTDkcCtwHPL/uhhgzLE4ExswRSfsBfwK8BHiHpKfX2yJjhsOJwJg5oKy+9jnA2yObmet04Ix6W2XMcDgRGDM33gzcGxFX5q8/DjxX0strbJMxQ2HXkDHGdBzfERhjTMdxIjDGmI7jRGCMMR3HicAYYzqOE4ExxnQcJwJjjOk4TgTGGNNx/j9wjcGYg7T3jQAAAABJRU5ErkJggg==\n",
      "text/plain": [
       "<Figure size 432x288 with 1 Axes>"
      ]
     },
     "metadata": {
      "needs_background": "light"
     },
     "output_type": "display_data"
    }
   ],
   "source": [
    "plt.scatter(scatter_X,scatter_A)\n",
    "plt.xlabel('X')\n",
    "plt.ylabel('A')\n",
    "plt.title('Two Phase Region vs. Stress at a given Temp')\n",
    "plt.show()"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": null,
   "id": "762945ef",
   "metadata": {},
   "outputs": [],
   "source": []
  }
 ],
 "metadata": {
  "kernelspec": {
   "display_name": "Python 3 (ipykernel)",
   "language": "python",
   "name": "python3"
  },
  "language_info": {
   "codemirror_mode": {
    "name": "ipython",
    "version": 3
   },
   "file_extension": ".py",
   "mimetype": "text/x-python",
   "name": "python",
   "nbconvert_exporter": "python",
   "pygments_lexer": "ipython3",
   "version": "3.9.12"
  }
 },
 "nbformat": 4,
 "nbformat_minor": 5
}
