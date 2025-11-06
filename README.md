# fresh-air-shop
fresh air shop 
import { Minus, Plus, X } from 'lucide-react';
import { Button } from '@/components/ui/button';
import { Sheet, SheetContent, SheetHeader, SheetTitle, SheetTrigger } from '@/components/ui/sheet';
import { useCartStore } from '@/lib/cart-store';
import { Checkbox } from '@/components/ui/checkbox';
import { Label } from '@/components/ui/label';
import { useState } from 'react';
import { useNavigate } from 'react-router-dom';

export default function Cart() {
  const { items, removeItem, updateQuantity } = useCartStore();
  const navigate = useNavigate();
  const [includeGiftBox, setIncludeGiftBox] = useState(false);
  const [includeGiftWrap, setIncludeGiftWrap] = useState(false);
  const [includeSurprisePackage, setIncludeSurprisePackage] = useState(false);

  const subtotal = items.reduce((sum, item) => sum + item.price * item.quantity, 0);
  const giftBoxPrice = includeGiftBox ? 99 : 0;
  const giftWrapPrice = includeGiftWrap ? 79 : 0;
  const surprisePackagePrice = includeSurprisePackage ? 79 : 0;
  const total = subtotal + giftBoxPrice + giftWrapPrice + surprisePackagePrice;

  const handleCheckout = () => {
    navigate('/checkout');
  };

  return (
    <Sheet>
      <SheetTrigger asChild>
        <Button variant="outline" size="icon" className="relative">
          <svg
            xmlns="http://www.w3.org/2000/svg"
            width="24"
            height="24"
            viewBox="0 0 24 24"
            fill="none"
            stroke="currentColor"
            strokeWidth="2"
            strokeLinecap="round"
            strokeLinejoin="round"
          >
            <circle cx="8" cy="21" r="1" />
            <circle cx="19" cy="21" r="1" />
            <path d="M2.05 2.05h2l2.66 12.42a2 2 0 0 0 2 1.58h9.78a2 2 0 0 0 1.95-1.57l1.65-7.43H5.12" />
          </svg>
          {items.length > 0 && (
            <span className="absolute -top-2 -right-2 bg-primary text-primary-foreground rounded-full w-5 h-5 text-xs flex items-center justify-center">
              {items.reduce((sum, item) => sum + item.quantity, 0)}
            </span>
          )}
        </Button>
      </SheetTrigger>
      <SheetContent className="w-full sm:max-w-lg">
        <SheetHeader>
          <SheetTitle>Handlekurv</SheetTitle>
        </SheetHeader>
        <div className="flex flex-col h-full">
          <div className="flex-1 overflow-y-auto py-4">
            {items.length === 0 ? (
              <p className="text-center text-muted-foreground py-8">Handlekurven er tom</p>
            ) : (
              <div className="space-y-4">
                {items.map((item) => (
                  <div key={item.id} className="flex gap-4 border-b pb-4">
                    <img src={item.image} alt={item.name} className="w-20 h-20 object-cover rounded" />
                    <div className="flex-1">
                      <h3 className="font-semibold">{item.name}</h3>
                      <p className="text-sm text-muted-foreground">{item.price} kr</p>
                      <div className="flex items-center gap-2 mt-2">
                        <Button
                          variant="outline"
                          size="icon"
                          className="h-8 w-8"
                          onClick={() => updateQuantity(item.id, Math.max(0, item.quantity - 1))}
                        >
                          <Minus className="h-4 w-4" />
                        </Button>
                        <span className="w-8 text-center">{item.quantity}</span>
                        <Button
                          variant="outline"
                          size="icon"
                          className="h-8 w-8"
                          onClick={() => updateQuantity(item.id, item.quantity + 1)}
                        >
                          <Plus className="h-4 w-4" />
                        </Button>
                      </div>
                    </div>
                    <Button
                      variant="ghost"
                      size="icon"
                      onClick={() => removeItem(item.id)}
                    >
                      <X className="h-4 w-4" />
                    </Button>
                  </div>
                ))}

                {/* Gift Options */}
                <div className="space-y-3 pt-4 border-t">
                  <h3 className="font-semibold text-sm">Tilleggstjenester</h3>
                  
                  <div className="flex items-center space-x-2 p-3 bg-muted/50 rounded-lg">
                    <Checkbox 
                      id="cart-giftbox"
                      checked={includeGiftBox}
                      onCheckedChange={(checked) => setIncludeGiftBox(checked as boolean)}
                    />
                    <Label 
                      htmlFor="cart-giftbox"
                      className="text-sm cursor-pointer flex-1"
                    >
                      Eksklusiv gave eske (+99 kr)
                    </Label>
                  </div>

                  <div className="flex items-center space-x-2 p-3 bg-muted/50 rounded-lg">
                    <Checkbox 
                      id="cart-giftwrap"
                      checked={includeGiftWrap}
                      onCheckedChange={(checked) => setIncludeGiftWrap(checked as boolean)}
                    />
                    <Label 
                      htmlFor="cart-giftwrap"
                      className="text-sm cursor-pointer flex-1"
                    >
                      Gavepapir (+79 kr)
                    </Label>
                  </div>

                  <div className="flex items-center space-x-2 p-3 bg-muted/50 rounded-lg">
                    <Checkbox 
                      id="cart-surprise"
                      checked={includeSurprisePackage}
                      onCheckedChange={(checked) => setIncludeSurprisePackage(checked as boolean)}
                    />
                    <Label 
                      htmlFor="cart-surprise"
                      className="text-sm cursor-pointer flex-1"
                    >
                      Overraskelsespakke (+79 kr)
                    </Label>
                  </div>
                </div>
              </div>
            )}
          </div>
          
          {items.length > 0 && (
            <div className="border-t pt-4 space-y-2">
              <div className="flex justify-between text-sm">
                <span>Delsum</span>
                <span>{subtotal.toFixed(2)} kr</span>
              </div>
              {includeGiftBox && (
                <div className="flex justify-between text-sm text-muted-foreground">
                  <span>Eksklusiv gave eske</span>
                  <span>+{giftBoxPrice} kr</span>
                </div>
              )}
              {includeGiftWrap && (
                <div className="flex justify-between text-sm text-muted-foreground">
                  <span>Gavepapir</span>
                  <span>+{giftWrapPrice} kr</span>
                </div>
              )}
              {includeSurprisePackage && (
                <div className="flex justify-between text-sm text-muted-foreground">
                  <span>Overraskelsespakke</span>
                  <span>+{surprisePackagePrice} kr</span>
                </div>
              )}
              <div className="flex justify-between font-bold text-lg pt-2 border-t">
                <span>Totalt</span>
                <span>{total.toFixed(2)} kr</span>
              </div>
              <Button className="w-full" size="lg" onClick={handleCheckout}>
                Gå til kassen
              </Button>
            </div>
          )}
        </div>
      </SheetContent>
    </Sheet>
  );
}
