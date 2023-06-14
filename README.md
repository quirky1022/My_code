from __future__ import annotations # PEP 563: Postponed Evaluation of Annotations
from dataclasses import dataclass # https://docs.python.org/3/library/dataclasses.html I like these a lot

@dataclass
class Curve:
    """
    Elliptic Curve over the field of integers modulo a prime.
    Points on the curve satisfy y^2 = x^3 + a*x + b (mod p).
    """
    p: int # the prime modulus of the finite field
    a: int
    b: int

# secp256k1 uses a = 0, b = 7, so we're dealing with the curve y^2 = x^3 + 7 (mod p)
bitcoin_curve = Curve(
    p = 0xFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFEFFFFFC2F,
    a = 0x0000000000000000000000000000000000000000000000000000000000000000, # a = 0
    b = 0x0000000000000000000000000000000000000000000000000000000000000007, # b = 7
)
@dataclass
class Point:
    """ An integer point (x,y) on a Curve """
    curve: Curve
    x: int
    y: int

G = Point(
    bitcoin_curve,
    x = 0x79BE667EF9DCBBAC55A06295CE870B07029BFCDB2DCE28D959F2815B16F81798,
    y = 0x483ada7726a3c4655da4fbfc0e1108a8fd17b448a68554199c47d08ffb10d4b8,
)

# we can verify that the generator point is indeed on the curve, i.e. y^2 = x^3 + 7 (mod p)
print("Generator IS on the curve: ", (G.y**2 - G.x**3 - 7) % bitcoin_curve.p == 0)

# some other totally random point will of course not be on the curve, _MOST_ likely
import random
random.seed(1337)
x = random.randrange(0, bitcoin_curve.p)
y = random.randrange(0, bitcoin_curve.p)
print("Totally random point is not: ", (y**2 - x**3 - 7) % bitcoin_curve.p == 0)
